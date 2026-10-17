# Review checklist: security.jcady.org

For anyone editing, reviewing or testing this site: Jeff Cady's security consulting site, published by GitHub Pages
from the `main` branch to security.jcady.org (see `CNAME`). It's static HTML with no build step, no sign-in and no
server of its own. The same checklist appears on every pull request (`.github/pull_request_template.md`).

Copyright © 2026 JCady LLC. All rights reserved.

## 1. Before you start

- Read `THEME.md` (the shared design system: tokens, classes, how to start a page) and `docs/ARCHITECTURE.md` (the
  pages and where each form sends data).
- Work on a branch and open a pull request. **Merging to `main` publishes the site.** Jeff reviews and merges.

## 2. Test before review

- Open each changed page in a browser, at desktop width and at phone width (about 400px). Nothing should scroll
  sideways.
- Check light and dark mode (`theme.js` follows the system setting).
- Click every changed link, including links to familysecurityplaybook.com and its business site.
- Submit each changed form once with test details and tell Jeff, so he can delete the test entry.
- Check the browser console for errors.

## 3. Forms and outside services

- Contact forms post to **Formspree**; embedded forms come from **HubSpot**. Don't change their form IDs, portal ID or
  addresses without Jeff: a wrong ID sends leads nowhere, with no visible error.
- Don't add trackers, ad pixels, chat widgets or other third-party scripts without Jeff's OK, and update
  `SECURITY.md` ("Privacy by design") if what the site collects changes.
- Scripts and fonts load only from the hosts already in use.

## 4. Content rules

- Keep the JCady LLC copyright line.
- No names of clients, employers or insurers covered by a confidentiality agreement, in case stories or examples.
- No logos, member IDs or wording that implies endorsement by a government or law-enforcement agency or program.
- Bios list only the credentials on record (CISSP, FEMA ICS, CERT, amateur radio license), and no degrees.
- Never promise insurance coverage, compliance or getting money back.
- Don't publish tax or account identifiers for JCady LLC.
- The JCady Toolkit is not offered on this site until the founders round.
- Plain, specific language. Examples use made-up companies and 555 numbers.

## 5. Design rules

- Build pages from `theme.css` and `theme.js`; page-only styles go in the page's own `<style>` after the theme link.
- A change to `theme.css` or `theme.js` also has to be copied to every other jcady.org site that uses them (see
  `THEME.md`).

## 6. Hosting

- `jcady.org` DNS stays at Hover. Don't move it or change the `CNAME` file without Jeff.
- After merging, check the live pages once GitHub Pages has published (usually a minute or two).

## Who to ask

Jeff Cady, JCady LLC: jeff@jcady.org. Security issues: see `SECURITY.md`.
