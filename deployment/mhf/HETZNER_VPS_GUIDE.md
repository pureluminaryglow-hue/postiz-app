# Hetzner Cloud VPS setup — Marion Hughes Foundation

> **Status:** Installation instructions only. This repository does not own a VPS, domain, OAuth apps, or social channels. A human account owner must purchase hosting and authorize the providers. Use staging first; the current Compose template is not independently production-audited.

## Server order: recommended choices
- Cloud provider: Hetzner Cloud; use an x86 CX43 (8 shared vCPU, 16 GB RAM, 160 GB SSD), subject to availability, current price and service requirements.
- Operating system: Ubuntu 24.04 LTS (x86_64).
- Region: choose a compliant location with acceptable latency and data residency. The public-facing mission is in South Africa, but hosting in the EU has international transfer/privacy implications to assess.
- Authentication: SSH public key, not password-only root login.
- Hetzner Cloud Firewall: allow TCP 22 **from the admin's own IP range only**, TCP 80 and 443 from the public internet; deny other inbound ports.
- Enable paid server backups if desired, and **also** maintain independently tested encrypted off-server database/volume backups. Snapshots alone are insufficient.

Do **not** share root or Postiz passwords via GitHub, ChatGPT, messages or screenshots.

## First-login security (once provisioned)
Connect from a trusted terminal with your own key:

```bash
ssh root@YOUR_SERVER_IP
apt-get update && apt-get upgrade -y
apt-get install -y ufw fail2ban git curl ca-certificates
adduser mhfadmin
usermod -aG sudo mhfadmin
install -d -m 700 -o mhfadmin -g mhfadmin /home/mhfadmin/.ssh
cp /root/.ssh/authorized_keys /home/mhfadmin/.ssh/authorized_keys
chown mhfadmin:mhfadmin /home/mhfadmin/.ssh/authorized_keys
chmod 600 /home/mhfadmin/.ssh/authorized_keys
```

**Test SSH as `mhfadmin` in a second terminal before changing SSH settings or closing the root session.** Once verified, disable root login and password authentication in an SSHD drop-in, validate with `sshd -t`, then reload ssh service. Restrict port 22 in the provider firewall to your trusted IP and ensure you have an emergency console recovery method. Do not lock yourself out.

### Docker
Use Docker's **official Ubuntu repository installation instructions**, not unofficial copy/paste shell scripts:
https://docs.docker.com/engine/install/ubuntu/

Install Docker Engine and the Docker Compose plugin; verify:
```bash
sudo docker version
sudo docker compose version
```
Docker group membership gives root-equivalent host access. Prefer the least privileged appropriate operator workflow.

### Domain and TLS
1. Own and control a domain; create an `A` record such as `social.YOUR_OWN_DOMAIN` pointing to the VPS IP.
2. In your terminal, verify DNS resolving to the intended server before enabling OAuth redirect URLs.
3. Install Caddy using the official instructions: https://caddyserver.com/docs/install
4. Configure an HTTPS reverse proxy in `/etc/caddy/Caddyfile`:

```caddyfile
social.YOUR_OWN_DOMAIN {
    encode zstd gzip
    reverse_proxy 127.0.0.1:4007
}
```

5. Validate with `sudo caddy validate --config /etc/caddy/Caddyfile` and reload Caddy. Ensure ports 80/443 are open so certificate issuance/renewal can work.
6. Keep Postiz bound to localhost via `127.0.0.1:4007:5000`. PostgreSQL, Redis, Temporal and Elasticsearch **must not** be accessible publicly.

### Deploy this repository
As a privileged operator, clone from the repository (or check out the branch if the deployment pull request is not yet merged):

```bash
git clone https://github.com/pureluminaryglow-hue/postiz-app.git
cd postiz-app
# If pull request #2 is still open:
git fetch origin setup/mhf-self-hosted-postiz
git checkout setup/mhf-self-hosted-postiz
cd deployment/mhf
cp .env.example .env
chmod 600 .env
```

Edit `.env` **on the VPS**, not in GitHub. Enter your real HTTPS URL and locally generated random credentials:

```bash
openssl rand -hex 32      # JWT secret
openssl rand -base64 32   # encryption key
openssl rand -hex 24      # separate password for Postiz PostgreSQL
openssl rand -hex 24      # separate password for Temporal PostgreSQL
```

Keep these secrets in a password manager and encrypted backup. Do not paste the output into chat. If the developer OAuth apps are not configured yet, leave their variables empty.

```bash
sudo docker compose --env-file .env -f compose.yaml config --quiet
sudo docker compose --env-file .env -f compose.yaml up -d
sudo docker compose --env-file .env -f compose.yaml ps
```

The site should eventually appear at `https://social.YOUR_OWN_DOMAIN` if DNS, TLS and services start successfully. Test the login, scheduled jobs, media storage and restart recovery. If you must inspect logs, keep secrets out of pasted output.

Create the initial administrator account **before** changing `DISABLE_REGISTRATION=true` in `.env`, then recreate the service. If site setup depends on email, configure a verified email provider first.

### Production security gates (required before launch)
- Review upstream Postiz version-specific instructions for Temporal, OAuth and encryption compatibility; the bundled `temporalio/auto-setup` and development dynamic config are not a hardened production architecture.
- Pin approved container image versions/digests; evaluate image vulnerabilities, backups and regular patching.
- Set host hardening (least privilege, SSH controls, automatic security updates, intrusion detection, monitoring, rate limiting and alerts).
- Encrypt secrets outside Git; protect database and media containing account tokens and personal data.
- Verify privacy/retention and international transfer obligations under POPIA for South African donors and beneficiaries.
- Test remote encrypted backups of every persistent volume and restore in a separate isolated environment.
- Check a real Postiz post in draft/approved mode before enabling scheduled publishing.
- Only add social OAuth client secrets after respective provider authorization, never into the repository.

### Social connections after a working HTTPS deployment
1. Register official Facebook Page and Instagram professional account, authorize Meta integration.
2. Set up LinkedIn Organization Page, TikTok account, YouTube channel, Threads, Pinterest business and X accounts under the foundation.
3. Use Postiz's provider-specific connection screen and **the exact callback URLs it displays**; register those in each provider's developer console as required.
4. Add approved client IDs/secrets to `deployment/mhf/.env` on the server. Recreate only the app service, then approve OAuth for each account.
5. Verify each channel's name, permissions and a low-risk test post. Do not enable automatic cross-posting without review.

**Fundraising controls:** describe Marion Hughes Foundation as an initiative while registration remains in process; never state tax-deductible status or operating facility capacity until verified. Use the exact verified BackaBuddy campaign URL. Obtain appropriate permissions for any identifiable vulnerable adult story, image or care details.

## Operating cost
Budget for the VPS plus IPv4, backups, domain renewal, external storage, and possible platform developer API subscription charges. Self-hosting Postiz does not eliminate costs or OAuth approval rules. Verify the total provider checkout bill before purchasing.
