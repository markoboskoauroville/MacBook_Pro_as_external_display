# TAKEOVER

Where the work stands, for the next session.

- **State (21.10.2026): plan only, no code; the project is closed for now.** The plan is Marko's: a USB-C dummy display plug on the sender (the 16-inch M1 Pro), a Thunderbolt 4 cable to the 14-inch M1 Pro (this Mac, the screen, with the BenQ), a Swift sender speaking the Accsoon format, and CINEVIEW_VIEWER as the receiver. PLAN.md lists its holes and how each is closed.
- **Web page:** https://marko-macbook-pro-as-external-display.pages.dev (Cloudflare Pages project `marko-macbook-pro-as-external-display`, source `site/`). It has the three schematics, the port map, the shopping list with Amazon.de links, the holes and the order of work.
- **Deploy:** `npx wrangler@3 pages deploy site --project-name marko-macbook-pro-as-external-display --branch main`, with CLOUDFLARE_ACCOUNT_ID and the token from the API folder in the environment (the SHOP_FINDER deploy.sh way), never printed.
- **Waiting for Marko:** buying the dummy plug (B0FCDJG6SW) and the Thunderbolt 4 cable (B08KSM2FWR) from the shopping list.
- **Next, when he reopens it:** step 1, CINEVIEW_VIEWER's first build and run on the 14-inch, with the 16-inch's HDMI going into the Nano. Then Mantra Display sender v1.
- **Never tested:** everything; nothing is built yet.
