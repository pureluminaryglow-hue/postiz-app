# Self-hosted Postiz — Marion Hughes Foundation

This directory is an **unverified deployment template**, not a live deployment. It is separate from the repository's developer-oriented root `docker-compose.yaml` and does not contain passwords or social-media OAuth tokens.

**Organizational identity:** Marion Hughes Foundation  
**First project:** Brandon's Home  
**Contact:** brandonshome@marionhughesfoundation.co.site  
**Timezone:** Africa/Johannesburg

## Prerequisites
- A Linux server/VPS that you administer with Docker Engine + modern Docker Compose.
- Sufficient RAM/CPU/disk for Postiz, PostgreSQL, Redis, Temporal and Elasticsearch. Confirm resource requirements against current Postiz documentation before purchasing hosting.
- A domain or subdomain you control, e.g. `postiz.your-own-domain.example`, with a DNS record to the server.
- HTTPS reverse proxy with an automatically renewed TLS certificate (Caddy, nginx or a managed ingress).
- Private, encrypted off-site backups of PostgreSQL and all named storage volumes; a tested restore procedure.
- Access control, patch management, host firewall, server monitoring and incident response plan.

**Important:** The bundled Temporal `auto-setup` component and development dynamic configuration follow the upstream sample and are not independently hardened for long-term public production use. Review and adapt the Temporal configuration/architecture before relying on this for a public nonprofit service. Use staging testing first.

## Deploy to a private staging server
1. Clone this GitHub repository and navigate to `deployment/mhf` on the server.
2. Copy the safe template: `cp .env.example .env`. This must be executed in `deployment/mhf`.
3. Create strong **different** secrets, e.g. `openssl rand -hex 32` for JWT, `openssl rand -base64 32` for `POSTIZ_ENCRYPTION_KEY`, and `openssl rand -hex 24` for each database password. Never paste secrets into GitHub or ChatGPT.
4. Set `POSTIZ_PUBLIC_URL` to the exact HTTPS address you will access.
5. Secure .env: `chmod 600 .env`. Confirm `.env` is excluded by Git and never staged for commit.
6. Run `docker compose --env-file .env -f compose.yaml config` and review the rendered configuration **privately** (the output includes secrets; never paste it into an issue/chat/log).
7. Start with `docker compose --env-file .env -f compose.yaml up -d` and check `docker compose --env-file .env -f compose.yaml ps`. Logs may contain sensitive data: inspect locally, avoid public log sharing.
8. Put TLS reverse proxy in front of `127.0.0.1:4007` and verify a valid certificate; only allow ports 80/443 to the proxy (not PostgreSQL, Redis, Elasticsearch or Temporal).
9. Register the foundation administrator through the site. Then set `DISABLE_REGISTRATION=true` in your **private** .env, and restart/recreate Postiz. Confirm admin login still works.
10. Test posting, scheduled jobs, media persistence, server restarts and backups *before* launch.

**Do not run this in production unchanged.** Replace floating images with reviewed pinned versions, establish periodic upgrades and integrity-tested backups, audit security hardening and verify Temporal dependencies. Determine whether Postiz now offers a supported production deployment method newer than this repository.

## Important caveats
- This template uses local persistent media storage; plan capacity and backups or configure the upstream-supported object storage alternative.
- If social-provider credentials are necessary, create the developer applications in each provider's console; populate the corresponding variable in your private .env. OAuth redirect/callback URLs depend on the deployed Postiz version and must be taken from its integration screen/docs, not guessed.
- A social login **does not** transfer account ownership to Postiz. Each account/page must exist and its owner/admin must approve the OAuth flow.
- The encryption key protects stored channel tokens; back it up securely. Losing it may require reconnecting all platforms.
- Confirm compatibility of current images, startup orchestration, app version and server architecture on staging. This file was not executed against a live host.

## Platform onboarding
| Platform | Foundation account | External authorization | Postiz status |
| --- | --- | --- | --- |
| Facebook | Marion Hughes Foundation Page | Meta business permissions / Meta app if required | Not connected |
| Instagram | Marion Hughes Foundation professional account | Meta account/Page authorization | Not connected |
| TikTok | Foundation account | TikTok developer/app permission where applicable | Not connected |
| YouTube | Foundation channel | Google OAuth/developer app where applicable | Not connected |
| LinkedIn | Organization Page | LinkedIn admin and app permission | Not connected |
| Threads | Foundation profile | Meta/Threads integration | Not connected |
| Pinterest | Foundation business account | Pinterest authorization | Not connected |
| X | Foundation account | X developer app and applicable access | Not connected |

### After onboarding
- In Postiz, find the Channels/Integrations area and connect one channel at a time.
- Validate identity, access and a low-risk draft/test on each platform.
- Use the campaign draft pack in `foundation-social/` (pending PR #1 merge) for your approved content.
- Do not publish foundation-registration, tax-deduction or operational-status claims unless proven.
- Do not post identifiable individuals' sensitive details without appropriate informed consent.
- Keep the donation link the exact verified BackaBuddy campaign URL, not a guessed address.
- Default to review-before-publish and document who is authorized to approve nonprofit statements.

Official references: https://docs.postiz.com and https://github.com/gitroomhq/postiz-app
