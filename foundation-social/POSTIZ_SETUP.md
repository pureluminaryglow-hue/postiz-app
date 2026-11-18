# Postiz deployment and platform connection guide

**Current state:** Repository source reviewed; no running Postiz URL, live deployment, social authorizations, or production API credentials have been provided or verified.

## Choose hosting
Option A: **Postiz Cloud** (https://platform.postiz.com) — managed hosting; connect social accounts via its user interface. This repository is not required for that option.

Option B: **Self-hosted Postiz** — run a production deployment using official instructions (https://docs.postiz.com). This repository has `docker-compose.yaml`, but its defaults are intended for localhost and contain example values. Do not deploy that file to the internet unchanged.

## Production readiness checklist
- [ ] Choose Cloud or an owned HTTPS hosting domain and server.
- [ ] Establish domain/DNS, valid TLS, persistent storage, PostgreSQL, Redis and any other services required by this Postiz version.
- [ ] Generate a strong JWT secret and unique database credentials. Place all secrets in hosting secret storage, not version control.
- [ ] Configure `MAIN_URL`, `FRONTEND_URL`, `NEXT_PUBLIC_BACKEND_URL` and relevant internal service URLs for the actual deployment.
- [ ] Configure user registration controls, admin identity, two-factor authentication when supported, backups, monitoring and updates.
- [ ] Complete and test installation, database migrations, uploads, scheduled publishing and recovery procedures.
- [ ] Create or validate the official Marion Hughes Foundation social profiles on the platforms themselves; use the official foundation email and unique credentials stored securely.
- [ ] Connect each platform to Postiz via the official OAuth/authorization UI. Depending on hosting mode and platform, developer app credentials, app review or approved permissions may be required.
- [ ] Check that Postiz displays the intended account/page identity and publishing permission for each channel.
- [ ] Schedule a private/unlisted or low-risk test where supported; verify formatting, links and timestamps before approving public launch.
- [ ] Add another trusted foundation administrator and document credential recovery.
- [ ] Keep all posts in approval/draft status until the owner authorizes first publication.

## Platform connection checklist
| Platform | Profile type / connection note | Status |
| --- | --- | --- |
| Facebook | Official Page; use a dedicated Meta business portfolio | Not connected |
| Instagram | Professional profile; connect through Meta as required | Not connected |
| TikTok | Foundation account with required permissions | Not connected |
| YouTube | Organization-managed channel/Brand Account as available | Not connected |
| LinkedIn | Organization Page, controlled by appointed admins | Not connected |
| Threads | Instagram-linked account where required | Not connected |
| Pinterest | Business account | Not connected |
| X | Account and applicable API authorization | Not connected |
| WhatsApp Business | Separate donor-support and consent flow; do not assume Postiz channel support | Not connected |

## Data and fundraising precautions
- Keep the existing BackaBuddy fundraising URL verified before any campaign link is published.
- Do not claim tax deductibility or registered NGO/NPO status before the respective approvals.
- Use clear donor privacy wording and separate marketing opt-ins consistent with South Africa's POPIA.
- Never send client secrets, passwords or platform tokens in GitHub issues, pull requests, commits or chat.
- Because the Postiz source in this repo is open source and may change, check the official version-specific docs for social platform setup.

## Done means
A running HTTPS Postiz installation, successful test login, individual platform account authorizations, a verified donation URL, and one owner-approved test post per connected account. **No item above is marked complete merely by merging this documentation.**