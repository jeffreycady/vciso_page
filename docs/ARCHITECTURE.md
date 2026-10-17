# security.jcady.org: architecture

UML diagrams for this site, with the overview of every JCady system. Generated from docs/uml/diagrams.py in the familysecurityplaybook repository by docs/uml/make.py; change them there.

## Overview

### System context and deployment

*Activity / component diagram.* Every deployed system, who uses it, and the outside services each one calls. Three Cloudflare-hosted applications and one static site.

```mermaid
flowchart LR
  subgraph People
    FAM["Family or household"]
    ORG["Organization staff<br/>credit union, church, HOA"]
    SHOP["Repair shop or MSP tech"]
    FIRM["Consulting firm user"]
    JEFF["Jeff, administrator"]
  end
  subgraph CF["Cloudflare"]
    subgraph PG["Worker: playbook-gate"]
      FAMSITE["familysecurityplaybook.com<br/>family site, builders, /app"]
      BIZSITE["business.familysecurityplaybook.com<br/>business site, /partner org pages"]
      ASSETS[("Static assets<br/>public/")]
      GATE[("KV: GATE")]
      CRON(["Daily cron 15:17 UTC"])
    end
    subgraph TK["Worker: jcady-toolkit (hosted)"]
      TKAPP["Toolkit home, tools, /team, /admin"]
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
  IDX & ER & INS & RC -- contact forms --> FS["Formspree"]
  IDX & INS & RC -- embedded forms --> HS["HubSpot forms"]
  DEMO -. links .-> FSP["familysecurityplaybook.com"]
  FS --> MAILJ["Email to Jeff"]
  HS --> CRM["HubSpot contacts"]
```
