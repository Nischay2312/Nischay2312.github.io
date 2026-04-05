---
layout: post
title: I Put My Entire PC Screen on a Watch (And It Actually Works)
subtitle: ScreenStreamWatch — ESP32-S3 AMOLED, Wi‑Fi, JPEG streaming, and a Python desktop server
thumbnail-img: https://img.youtube.com/vi/ACfcJynbRjY/maxresdefault.jpg
gh-repo: Nischay2312/ScreenStreamWatch
gh-badge: [star, fork, follow]
tags: [ESP32-S3, Waveshare, AMOLED, ESP-IDF, LVGL, Python, mDNS, JPEG, Screen Mirroring, Wi-Fi]
readtime: true
comments: true
---

This project is the latest step in something I’ve been tinkering with for a while: getting a **live view of my computer screen** onto a **small embedded display** over the wireless LAN. Earlier versions used a breadboard ESP32 + TFT with **WebSockets** and **UDP** ([first write-up](https://nischay2312.github.io/2023-08-06-Live-Streaming-Computer-Screen-Via-ESP32/), [improved streamer](https://nischay2312.github.io/2023-08-14-ESP32-Screen-Stream-Improved/)). **ScreenStreamWatch** goes further: a **watch-style ESP32-S3 board** with a **round AMOLED**, **touch**, proper **JPEG decode** on chip, **mDNS** discovery so you don’t hard-code IPs, and a **4-digit pairing** step so random devices on the network can’t latch onto your stream.

I made a video that walks through the hardware, the big-picture software story, and a full setup demo:

<p align="center">
  <a href="https://www.youtube.com/watch?v=ACfcJynbRjY" target="_blank" rel="noopener">
    <strong>Watch on YouTube → I Put My Entire PC Screen on a Watch (And It Actually Works)</strong>
  </a>
</p>

<div style="max-width: 800px; margin: 1.5rem auto;">
  <iframe width="560" height="315" style="width: 100%; max-width: 100%; aspect-ratio: 16 / 9; height: auto; min-height: 240px;" src="https://www.youtube.com/embed/ACfcJynbRjY" title="PC screen on ESP32-S3 watch" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
</div>

---

## Hardware

The firmware targets a specific kit: the **[Waveshare ESP32-S3 Touch AMOLED 2.06″](https://www.waveshare.com/wiki/ESP32-S3-Touch-AMOLED-2.06#Introduction)**. It’s not a generic “any ESP32 + any display” wiring job—the repo pulls the **Waveshare BSP** from Espressif’s component manager so the **round AMOLED**, **touch controller**, and **audio path** match the board.

At a high level the board gives you:

- **ESP32-S3** — dual-core MCU, **Wi‑Fi**, enough headroom to **decode JPEG** and run **LVGL** for the UI  
- **AMOLED** — full-color, high-contrast; the “entire PC screen” is really **your desktop resized and compressed into video frames**, but on the wrist it still reads clearly  
- **Capacitive touch** — navigate Wi‑Fi setup, discovery, pairing, and stream controls  
- **I²S codec + speaker** — optional **PCM audio** from the PC when you run the **AV** desktop script (muxed on the same TCP session)  
- **USB** — flash firmware and serial debug; day-to-day streaming is over **Wi‑Fi**

You’ll also want the watch and PC on the **same LAN**. **Guest Wi‑Fi** or **AP isolation** on the router often blocks **mDNS** or device-to-device traffic—if discovery fails, that’s the first place I check.

---

## Software architecture (bird’s-eye view)

There are two halves:

**On the PC (Python, in `tools/`):** capture the desktop (**DXCam** on Windows when available, **mss** otherwise), **resize** and **JPEG-encode** frames (**TurboJPEG** or OpenCV), run a small **TCP server** (default port **8765**), and advertise **`_screenstream._tcp`** with **Zeroconf** so the watch can find the host without typing an IP.

**On the watch (ESP-IDF, `main/`):** connect to Wi‑Fi (captive portal on first boot), **query mDNS** for that service, open a **TCP** connection, complete **pairing** (challenge/ACK), then **receive** either:

- **Legacy video:** repeated **32-bit little-endian length + JPEG** per frame, or  
- **AV mode:** a multiplexed **`SSAV`** packet stream with **video**, **PCM audio**, and **format** metadata—see `stream_app_config.h` and `screen_stream_server_av.py` for the details.

Decoded frames go to **RGB565** buffers and **LVGL** draws them to the display.

The project is pinned to **ESP-IDF v5.5.1** in the README; other IDF versions aren’t validated and may break configuration or build.

---

## Getting started (short version)

1. Install the **Espressif IDF** extension (VS Code / Cursor), run the setup wizard, select **ESP-IDF 5.5.1**, open the **repo root** (`CMakeLists.txt`), set target **esp32s3**, **build**, **flash**.  
2. First Wi‑Fi: join the device AP, open **http://192.168.4.1**, enter home network credentials.  
3. On the PC: `cd tools`, **Python venv**, `pip install -r requirements_screen_stream.txt`, run **`screen_stream_gui.py`** or **`screen_stream_server.py`**.  
4. Start the server, use the watch to **find the desktop**, confirm the **matching 4-digit code**, stream.

Full step-by-step text lives in the repository **[README](https://github.com/Nischay2312/ScreenStreamWatch/blob/main/README.md)** and **[tools/README.md](https://github.com/Nischay2312/ScreenStreamWatch/blob/main/tools/README.md)**.

---

## What to expect (and what not to)

This is **JPEG over Wi‑Fi** to a **small MCU**, not **HDMI**. You’ll see **latency**; **FPS** drops if you crank resolution or quality. The board’s **PSRAM** helps with large buffers, but **very large JPEGs** can still hit **`APP_MAX_JPEG_SIZE`** and similar limits in firmware. That’s normal for the class of project—tune **`--quality`**, **`--width`**, **`--height`**, and **`--fps`** on the Python side until it feels acceptable on your network.

---

## Source code and license

- **Repository:** [github.com/Nischay2312/ScreenStreamWatch](https://github.com/Nischay2312/ScreenStreamWatch)  
- Application code under **`main/`** and **`tools/`** is **AGPL-3.0** (see **`LICENSE`** in the repo). ESP-IDF, LVGL, Waveshare BSP, and other pulled components keep their own licenses.

If you build this or extend it (UI, audio, protocols), I’m curious what you come up with—leave a comment here or on the [YouTube video](https://www.youtube.com/watch?v=ACfcJynbRjY).
