# PLAN: the MacBook Pro as an external display

Written 21.10.2026. This is a plan only, and no code has been written.

## The screen we want to use

- **Receiver:** this MacBook Pro, model MacBookPro18,3: the 14-inch with the M1 Pro chip,
  a 3024 × 1964 Liquid Retina XDR screen at up to 120 Hz, and macOS 15.
- **The hard fact:** no port on it accepts a picture. The HDMI port only sends a picture out,
  and the Thunderbolt ports never accept DisplayPort in. Apple's Target Display Mode existed
  only on iMacs from 2009 to 2014, and Sidecar only uses an iPad as the second screen.
  So the picture must come in as **data**: a video stream over a cable or the network, or a
  USB capture card that turns HDMI into a camera.

## The question that decides the way

**Which machine sends its picture to the MacBook Pro?**

| Source | Best way |
|---|---|
| Another Mac (maybe the MacBook Air from the 18.10.2026 Amazon search) | way A or way D |
| A Windows or Linux PC | way C or way D |
| Anything with HDMI out (camera, console, mixer, a PC with no software) | way B |

Second question: **mirror or extend?** Mirroring shows the same picture on both screens.
Extending makes the MacBook Pro a true second desktop, and then the sender needs a
virtual display.

## The ways

### A. AirPlay to Mac (built in, Mac to Mac, nothing to buy)
On the sender, choose Control Centre → Screen Mirroring → this MacBook Pro, then
"Use as separate display". It works over Wi-Fi, or over a cable for less lag.
- Good: free, and ready in five minutes. It also extends the desktop, not only mirrors it.
- Against: it is an Apple service, and Freedom (21.10.2026) aims for a Mac without Apple.
  AirPlay Receiver may already be switched off in its steps. Over Wi-Fi it has lag and some
  compression.
- **Use it as the first test** to see whether the idea is worth building at all.

### B. A USB HDMI capture card (any source with HDMI)
HDMI out of the source → card → USB-C into the MacBook Pro. A small full-screen viewer
shows the picture.
- Good: works with any source and needs no software on it. Lag is about 50–100 ms, with no
  network involved.
- Against: the card must be bought. A cheap 1080p60 card costs about 15–25 €, and a 4K card
  costs more. The picture is compressed by the card, and the source sees a TV, not a
  Retina screen. The mouse stays with the source.
- Our part: **Capture Display (Mantra)**, a Swift app (AVFoundation + Metal). It opens the
  card and fills the screen at 1:1 with no scaling blur, and it has a stripe for
  input/resolution/latency.

### C. Ready-made streaming apps (software only)
- **Sunshine (host) + Moonlight (client):** open source, low lag, any OS as host. It extends
  the desktop with a virtual display or a dummy HDMI plug on the host.
- **Deskreen:** open source; the receiver is only a browser.
- **Duet Display / Luna Display:** paid. Luna is a hardware dongle with a Mac-to-Mac mode.
- Good: nothing to build, and we test on the same day. Against: someone else's app,
  someone else's updates.

### D. Our own: Mantra Display (Swift, both ends)
- **Sender (Mac):** creates a virtual display (the way BetterDisplay does it), grabs it with
  ScreenCaptureKit, and encodes it to HEVC with VideoToolbox in hardware.
- **Link:** a Thunderbolt Bridge cable (Mac to Mac, about 10–40 Gbit/s, almost no lag),
  wired Ethernet or Wi-Fi, through Network.framework.
- **Receiver (this MacBook Pro):** decodes in hardware and draws full screen with Metal at
  the native 3024 × 1964 and 120 Hz. Optional: the receiver's keyboard and trackpad control
  the sender.
- Good: ours, Apple-free, the best picture and the lowest lag on a cable.
- Against: the most work. The virtual display uses a private Apple API that a macOS update
  can break.

## The order of work

1. **Step 0: Marko answers** the two questions: which source, and mirror or extend.
2. **Step 1: a five-minute test** with way A (for a Mac source) or way C (for a PC source).
   It shows whether a MacBook Pro as a screen is really what he wants on his desk.
3. **Step 2: choose:**
   - a ready-made app is enough → we stop there; we document it in README and TAKEOVER
     and add it to the registry;
   - HDMI sources matter → way B: buy a card (one card, the smallest that does 1080p60) and
     build Capture Display (Mantra) v1;
   - a Mac source and Apple-free → way D: Mantra Display v1, mirror only, over a Thunderbolt
     cable. v2 adds the virtual display (extend), v3 adds the keyboard and mouse back,
     v4 adds Wi-Fi.
4. **Each version** is signed with "Mantra Apps", gets a row in the star, and is tested on
   the MacBook Pro screen (never the BenQ). Marko tests what needs a hand, asked by voice.
   Lag is measured with the Iriun camera filming both screens.

## What is not known yet

- Which machine is the source, and whether it is on the desk with a cable or across the room.
- Whether Freedom switched off AirPlay Receiver. It is a single check on the day of step 1.
- The lag he can live with: for video and DJ work about 100 ms is fine; for typing on the
  far machine it must be under about 30 ms, which only a cable gives.
