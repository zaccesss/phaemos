# phaemos.com website

**Why it matters:** The domain is owned and live but completely empty - the project has no public presence. A good landing page significantly increases the perceived professionalism for recruiters and portfolio viewers.

**Rough approach:**
- Separate repo or a `website/` folder in this repo
- Next.js (consistent with the dashboard) or a static site
- Pages needed:
  - Landing page: what PHAEMOS is, architecture diagram, key features, demo video embed
  - Docs: link to README and API reference, quickstart guide
  - Contact form: Resend for email delivery
  - Status page link (once Uptime Kuma is set up)
- CORS in the backend: add `https://phaemos.com` to `ALLOWED_ORIGINS`
- Deploy to Vercel with custom domain pointing to phaemos.com

**Priority:** medium - good for portfolio but not a blocker for the technical work
