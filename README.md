# MacBook Pro as external display

**Web page:** https://marko-macbook-pro-as-external-display.pages.dev (the schematic of the whole system and the Amazon.de links)

Use the MacBook Pro as an external display. Started 21.10.2026. The receiver is the MacBook Pro 14" (M1 Pro); no port on it takes a picture in, so the picture comes in as a stream or through a USB capture card.

The plan (Marko's, 21.10.2026): the 16-inch M1 Pro (sender) gets a USB-C dummy display plug, so macOS sees a real second screen. A Swift sender grabs that screen and sends it in the Accsoon format over a Thunderbolt 4 cable (Thunderbolt Bridge). The 14-inch M1 Pro (this Mac) shows it full screen in CINEVIEW_VIEWER.

Open **PLAN.md** for the plan and its holes, **SHOPPING.md** for what to buy (with the Amazon.de links), TAKEOVER.md for where it stands. The web page's source is `site/`.

## Status

- 21.10.2026: repository created (public); PLAN.md, SHOPPING.md, and the web page with three Nano Banana Pro schematics, the port map and the shopping list. Plan only, no code. Project closed for now.
