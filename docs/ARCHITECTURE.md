# security.jcady.org: architecture

UML diagrams for this site, with the overview of every JCady system.

Generated from docs/uml/diagrams.py in the familysecurityplaybook repository by docs/uml/make.py. Edit the source there, then regenerate.

## Overview

### System context and deployment

*Activity / component diagram.* Every deployed system, who uses it, and the outside services each one calls. Two Cloudflare Workers (five addresses) and one static site.

```mermaid
flowchart LR
  subgraph People
    FAM["Family or household"]
    ORG["Organization staff<br/>credit union, church, HOA"]
    SHOP["Repair shop or MSP tech"]
    FIRM["Consulting firm user"]
    PRO["Security professional<br/>(no account)"]
    JEFF["Jeff, administrator"]
  end
  subgraph CF["Cloudflare"]
    subgraph PG["Worker: playbook-gate"]
      FAMSITE["familysecurityplaybook.com<br/>family site, builders, /app"]
      BIZSITE["business.familysecurityplaybook.com<br/>business site, /partner org pages"]
      ASSETS[("Static assets<br/>public/")]
      GATE[("KV: GATE")]
      CRON(["Daily cron 15:17 UTC<br/>five jobs"])
    end
    subgraph TK["Worker: jcady-toolkit (MansioSec)"]
      TKAPP["app.mansiosec.com<br/>toolkit home, tools, /team, /admin"]
      TKSALES["www.mansiosec.com<br/>sales page, free Incident Timeline"]
      TKAPEX["mansiosec.com<br/>301 to www"]
      TKKV[("KV: TOOLKIT")]
      TKASSETS[("Assets: toolkit/")]
    end
    ACCESS["Cloudflare Access<br/>email PIN"]
  end
  VCISO["security.jcady.org<br/>static site, GitHub Pages"]
  STRIPE["Stripe<br/>checkout, subscriptions"]
  AUTH0["Auth0 jcady.us.auth0.com<br/>logins + MFA"]
  RESEND["Resend<br/>email"]
  LULU["Lulu Print API"]
  HUBSPOT["HubSpot CRM"]
  GOAT["GoatCounter<br/>page counts"]
  FORMS["Formspree + HubSpot forms"]
  FAM --> FAMSITE
  ORG --> BIZSITE
  SHOP --> BIZSITE
  FIRM --> TKAPP
  PRO --> TKSALES
  TKAPEX --> TKSALES
  TKSALES -. sign in .-> TKAPP
  JEFF --> ACCESS --> FAMSITE
  JEFF --> TKAPP
  FAMSITE --- ASSETS
  BIZSITE --- ASSETS
  FAMSITE --- GATE
  BIZSITE --- GATE
  CRON --> GATE
  TKAPP --- TKKV
  TKAPP --- TKASSETS
  FAMSITE --> STRIPE
  STRIPE -- signed webhooks --> FAMSITE
  BIZSITE --> AUTH0
  TKAPP --> AUTH0
  TKAPP -. fallback .-> ACCESS
  FAMSITE --> RESEND
  FAMSITE --> LULU
  LULU -- webhooks --> FAMSITE
  FAMSITE --> HUBSPOT
  ASSETS -.-> GOAT
  VCISO --> FORMS
  VCISO -. links .-> FAMSITE
  VCISO -. links .-> BIZSITE
  FAMSITE -. "Also from JCady LLC" .-> TKSALES
  TKSALES -. "Family Security Playbook" .-> FAMSITE
```

### Build and deploy pipeline

*Activity / component diagram.* How a change reaches production. Nothing is pushed to main directly: every change is a pull request that Jeff merges.

```mermaid
flowchart LR
  DEV["Change on a branch"] --> PR["Pull request on GitHub"]
  PR --> TESTS["node test/*.mjs<br/>unit and flow tests"]
  PR --> REVIEW{"Jeff reviews<br/>and merges"}
  REVIEW -- merge to main --> BUILD["Cloudflare Workers Builds"]
  BUILD --> WR["npx wrangler deploy"]
  WR --> LIVE["Live Worker + assets"]
  subgraph LB["Local build steps, committed with the change"]
    BPY["python3 build.py<br/>tool/*.html + engines + carry.js<br/>into src/tool.js, full.js, office.js, recovery.js"]
    HELP["tool/make_help.py<br/>+ make_help_pdf.mjs<br/>instructions pages and PDFs"]
    NOW["tool/make_help_now.py<br/>+ make_record_sample.mjs<br/>Something just happened guides, record sample PDF"]
    TKS["MansioSec: hosted/tools/make_site.py<br/>+ make_timeline_sample.mjs<br/>sales page, timeline and sample into src/site.js"]
    REL["MansioSec: docs/release.py<br/>VERSION, CHANGELOG, guides"]
  end
  DEV -.-> NOW
  DEV -.-> TKS
  DEV -.-> REL
  DEV -.-> BPY
  DEV -.-> HELP
  SECRETS[("Secrets set in the Cloudflare dashboard<br/>STRIPE_*, RESEND_API_KEY, ADMIN_KEY,<br/>AUTH0_CLIENT_SECRET, PARTNER_VAULT_KEY,<br/>LULU_*, HUBSPOT_TOKEN, SESSION_KEY")] -.-> LIVE
  NOTE["Toolkit tests: cd hosted && npm test.<br/>Only main deploys; branch preview builds can show a false red X."] -.- BUILD
```

## security.jcady.org

### security.jcady.org

*Activity / component diagram.* A static site with no sign-in. Forms go to Formspree and HubSpot; nothing is processed on JCady's own servers.

```mermaid
flowchart LR
  V["Visitor"] --> IDX["index.html<br/>vCISO, consulting, incident response"]
  V --> ER["er.html<br/>help for older adults after a scam"]
  V --> INS["insurance.html<br/>cyber insurance readiness"]
  V --> RC["rc.html<br/>security readiness check"]
  V --> DEMO["family-security-playbook-demo.html"]
  IDX --> OCK["Office checkup and sample deliverables"]
  OCK -. links .-> BCK["business.familysecurityplaybook.com/checkup"]
  ER -. "first-hour steps" .-> HN["familysecurityplaybook.com/help-now/"]
  ER & IDX -. "Write down what happened" .-> RECS["familysecurityplaybook.com/help-now/record.html"]
  ER & INS -- "Stripe payment links" --> ST["Stripe checkout"]
  IDX & ER & INS & RC -- contact forms --> FS["Formspree"]
  IDX & INS & RC -- embedded forms --> HS["HubSpot forms"]
  DEMO -. links .-> FSP["familysecurityplaybook.com"]
  FS --> MAILJ["Email to Jeff"]
  HS --> CRM["HubSpot contacts"]
```
