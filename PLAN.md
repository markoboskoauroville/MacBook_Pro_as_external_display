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

## Marko's plan (21.10.2026), checked: this is the plan

Marko's plan is way D below, with a dummy plug doing the job of the virtual display:

    SENDER (MacBook Pro M1 Pro, BenQ on HDMI)
      dummy display plug on a USB-C port -> macOS sees a real second screen
      Mantra Display sender (Swift): ScreenCaptureKit grabs that screen, VideoToolbox encodes HEVC,
      packets in the Accsoon format (MANTRA_MANIFEST modules/cineview-nano.md)
         | Thunderbolt cable -> Thunderbolt Bridge, a private network between the two Macs
    RECEIVER (the other MacBook Pro M1)
      CINEVIEW_VIEWER (Swift), already written for the Nano: decodes and fills the screen

**Why it is good:** the dummy plug removes the only fragile part of way D, the private API for
a virtual display. macOS treats the plug as a real monitor, so windows, Mission Control and
the mouse all work as they would on a real screen. With the Accsoon format, **one receiver
shows both the Nano and the Mac**, and CINEVIEW_VIEWER is already written for the Nano.

**The holes, and how each one is closed:**

1. **The display limit of the sender's chip.** An M1 Pro drives two external screens, so the
   BenQ plus the dummy is exactly its limit. A plain M1 (the 13-inch MacBook Pro or the
   MacBook Air M1) drives **only one**, so the BenQ plus the dummy will not work on it.
   → The sender is the M1 Pro, the Mac with the BenQ. Check which chip the other Mac has.
2. **The plug's resolutions.** A cheap dummy offers only the resolutions in its EDID (its
   built-in list of modes), usually 4K, 1440p and 1080p. If none matches the receiver's screen,
   the picture is scaled and soft. → Buy a plug whose EDID lists the receiver's resolution
   (13-inch: 2560 × 1600; 14-inch: 3024 × 1964), or use a HiDPI mode that is exactly half of
   it. A software virtual display (0 €) can be given any size, so it stays the fallback.
3. **The cable.** It must be a **Thunderbolt** cable with the lightning mark, 20 or 40 Gbps.
   It is not FireWire, and a USB-C charging cable makes no network. The Thunderbolt Bridge
   appears by itself with 169.254.x.x addresses, and the two apps find each other through
   Bonjour.
4. **The receiver does not "register as a display".** The dummy registers on the sender. The
   receiver only shows a full-screen window with no menu bar, Dock or sleep. The sender must
   include the mouse pointer in the stream.
5. **The Accsoon format** sends packets with no resend and drops late frames. On Wi-Fi this
   means torn frames. On a cable almost nothing is lost, so it is fine. The sender plays the
   role of the Nano: it listens on TCP 8001 and sends UDP to port 8000.
6. **CINEVIEW_VIEWER has never been compiled or run.** Its NOT TESTED list says so. It is
   the receiver, so its first run is step 1.
7. **The lag** is expected at about 30–50 ms (capture, encode, cable, decode, one frame).
   That is fine for watching and for most work, and fine for typing with the sender's own
   keyboard.

**The cheapest way to a second screen**, from what Marko already has:

| Way | Costs | Gives |
|---|---|---|
| A USB-C to HDMI adapter on the sender → **the Nano he owns** → Wi-Fi → CINEVIEW_VIEWER | about 10 € (0 € if an adapter is in the drawer) | a second screen at once, with no sender code. The Nano is the "dummy". Limits: 1080p, about 80–150 ms over Wi-Fi, and the receiver joins the Nano's Wi-Fi |
| A software virtual display + Thunderbolt | 0 € (plus the cable if he has none) | any resolution, but a private Apple API that a macOS update can break |
| **Marko's plan:** dummy plug + Thunderbolt | dummy about 8–15 €, Thunderbolt cable about 20–30 € | the best: real display, native sharp picture, lowest lag |

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

## The order of work (Marko's plan)

1. **Step 1: the receiver first, with the Nano.** The USB-C to HDMI adapter goes from the
   sender to the Nano, and the receiver runs CINEVIEW_VIEWER. This proves the receiver and the
   Accsoon format with no sender code, and it is already a second screen at 1080p.
2. **Step 2: Mantra Display sender v1.** Buy the dummy plug and a Thunderbolt cable if he has
   none. The sender grabs the dummy screen and speaks the Accsoon format over the cable, and
   CINEVIEW_VIEWER receives it by the Mac's address instead of 10.0.0.1. v2 adds HEVC at the
   receiver's native resolution, v3 adds the receiver's keyboard and trackpad, v4 adds
   Wi-Fi.
3. Each version is signed, put in the star and tested as below.

## The other order (before Marko's plan; kept for the choices)

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
