---
name: ec2-letsencrypt-https
description: >
  Set up Let's Encrypt HTTPS on an AWS EC2 Ubuntu instance for a new domain.
  Checks/installs certbot, inspects existing nginx vhosts without replacing them,
  adds a new port-80 vhost, validates DNS points to the public IP, and issues
  a cert with certbot --nginx. Use when the user says "link this domain to EC2",
  "set up let's encrypt", "enable https on this domain", "add one more domain
  with letsencrypt", or provides a domain plus an EC2 host.
compatibility: "Any agent with tailscale + ssh + sudo access to the EC2 target"
license: MIT
---

# EC2 Let's Encrypt HTTPS Setup

Add ONE more domain to an EC2 instance without touching existing domains.

## 0. Inputs to collect

- `DOMAIN` — e.g. `unified-website.duckdns.org`
- `TARGET` — EC2 host: Tailscale name/IP (`aws-fix`, `100.103.230.55`) or public IP (`3.85.61.184`)
- `SSH_USER` — usually `ubuntu`
- `WEBROOT` — existing site root if static (e.g. `/home/ubuntu/unified-website/public`), or `PROXY_PORT` if Node app (e.g. `3000`)
- `EMAIL` — for certbot `--agree-tos -m` (reuse `admin@duckdns.org` if user has none)

If the user only gives a domain, ask which EC2 instance; if only an instance, ask which domain.

## 1. Access (pem OR tailscale, prefer what works)

```bash
# Preferred: Tailscale SSH (no key needed, works if node is online)
tailscale status | grep -i "<TARGET>"
tailscale ssh <SSH_USER>@<TARGET> "hostname; whoami"

# Fallback: classic SSH with pem
ssh -i ~/Downloads/<key>.pem -o StrictHostKeyChecking=accept-new -o ConnectTimeout=8 <SSH_USER>@<PUBLIC_IP> "hostname"
```

Pick the first method that returns a hostname. Use it as `$SSH` prefix for all remote commands below.

## 2. Check certbot + nginx (install if missing)

```bash
tailscale ssh <SSH_USER>@<TARGET> "which certbot nginx; certbot --version 2>&1; nginx -v 2>&1"
```

If `certbot` missing (Ubuntu):

```bash
tailscale ssh <SSH_USER>@<TARGET> "sudo apt update && sudo apt install -y certbot python3-certbot-nginx"
```

If `nginx` missing: `sudo apt install -y nginx` then `sudo systemctl enable --now nginx`.

## 3. Inspect existing domains — NEVER replace

```bash
tailscale ssh <SSH_USER>@<TARGET> "ls -l /etc/nginx/sites-enabled/; sudo cat /etc/nginx/sites-enabled/* | grep -E 'server_name|ssl_certificate|root|proxy_pass' | head -n 40"
tailscale ssh <SSH_USER>@<TARGET> "sudo certbot certificates 2>&1 | head -n 40"
```

Record existing `server_name` values. The new domain gets its OWN file, e.g. `/etc/nginx/sites-available/<domain-slug>`, symlinked into `sites-enabled/`. Do not edit the 443 blocks of other domains.

## 4. DNS must point to the PUBLIC IP (not Tailscale IP)

Tailscale IPs (`100.64.0.0/10`) are not public — Let's Encrypt rejects them with `no valid A records`.

```bash
# From your Mac (public DNS):
dig +short <DOMAIN> @8.8.8.8
# From the EC2 (its public IP):
tailscale ssh <SSH_USER>@<TARGET> "curl -s --max-time 5 ifconfig.me; echo"
```

If DNS returns the Tailscale IP (`100.x`), tell the user: update DuckDNS/registrar A record to the public IP, wait 2–5 min, re-check with `dig @8.8.8.8` until it matches. Do NOT run certbot until they match.

## 5. Add port-80-only vhost for the new domain

Static site variant:

```bash
tailscale ssh <SSH_USER>@<TARGET> "sudo tee /etc/nginx/sites-available/<slug> > /dev/null <<'EOF'
server {
    listen 80;
    server_name <DOMAIN>;
    root <WEBROOT>;
    index index.html;
    location / {
        try_files \$uri \$uri/ \$uri.html =404;
    }
}
EOF
sudo ln -sf /etc/nginx/sites-available/<slug> /etc/nginx/sites-enabled/<slug>
sudo nginx -t && sudo systemctl reload nginx && echo OK"
```

Reverse-proxy variant (Node on port 3000):

```bash
location / {
    proxy_pass http://localhost:3000;
    proxy_http_version 1.1;
    proxy_set_header Upgrade \$http_upgrade;
    proxy_set_header Connection \"upgrade\";
    proxy_set_header Host \$host;
    proxy_set_header X-Real-IP \$remote_addr;
    proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto \$scheme;
}
```

## 6. Issue the certificate

```bash
tailscale ssh <SSH_USER>@<TARGET> "sudo certbot --nginx -d <DOMAIN> --non-interactive --agree-tos -m <EMAIL> --redirect 2>&1 | tail -n 15"
```

Expected success: `Successfully received certificate ... Congratulations! You have successfully enabled HTTPS`. Certbot rewrites the vhost to 443 + redirect automatically and enables auto-renew.

On `no valid A records` / challenge failure: stop, re-check step 4 (DNS + port 80 reachable from internet, security group allows 80/443).

## 7. Verify

```bash
curl -s -o /dev/null -w "HTTP:%{http_code} -> %{redirect_url}\n" --max-time 10 http://<DOMAIN>/
curl -s -o /dev/null -w "HTTPS:%{http_code} size:%{size_download}\n" --max-time 15 https://<DOMAIN>/
echo | openssl s_client -connect <DOMAIN>:443 -servername <DOMAIN> 2>/dev/null | openssl x509 -noout -dates -issuer
tailscale ssh <SSH_USER>@<TARGET> "sudo certbot certificates 2>&1 | grep -A 4 <DOMAIN>"
```

Report: HTTP→301, HTTPS→200, cert expiry (~90 days), auto-renew on, and confirm existing domains still serve.

## Pitfalls seen in the field

- DuckDNS set to `100.x` Tailscale IP instead of public IP — always compare `dig @8.8.8.8` vs `ifconfig.me`.
- Port 80 blocked by AWS security group — certbot HTTP-01 needs it open to `0.0.0.0/0`.
- Editing the wrong vhost and breaking an existing domain — always snapshot first: `sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/default.bak-$(date +%Y%m%d)`.
- Cert name collisions (`libp2p.duckdns.org` vs `-0001`) — check `certbot certificates` before re-issuing.
