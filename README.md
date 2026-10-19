# LAFIA: health-safety scanner for Burkina Faso

> **Laafi bee yãmb fãa**: *health for all of us* (Mooré)
>
> **First prize, INGENOVA 2025 · Orange Digital Center · Burkina Faso**
>
> Designed and built by **Brayan Sorgho**, solo.

Unsafe, expired or counterfeit food, medicines and cosmetics are hard for people in Burkina Faso to check. LAFIA turns any phone into a scanner: scan a product barcode, get a verdict in seconds, and see danger alerts on a live map of Ouagadougou.

**The source code is private.** This repository only presents the project. Access to the code can be granted on request: sorghobrayan60@gmail.com.

---

## What it does

- **Scan.** The phone camera reads the barcode locally (ZXing). There is nothing to install: it opens in Chrome.
- **Analyse.** The product is checked against open product data (Open Food Facts, Open Beauty Facts) and a curated list of substances of concern for Burkina Faso. It also gets a health score, an expiry check and a GS1 barcode authenticity check.
- **Verify packaging (optional AI).** A camera frame is compared with the official product photo to flag packaging that looks suspicious.
- **Alert.** Dangerous products create geolocated alerts on a live map.
- **Coverage.** Food, cosmetics, and 50 essential medicines (WHO list and Burkina Faso).

## Engineering choices

**One HTTPS origin, offline by design.** A single Node.js server serves the built front end, the REST API, the real-time scan relay (SSE) and the WebRTC signalling, all on one HTTPS port. Phones need HTTPS for the camera, and a single origin removes mixed-content failures. The full demo runs **without internet**: the laptop becomes a Windows hotspot, the phone joins it and scans a QR code to connect.

**Safety rule: a verdict can only get more severe.** A fresh scan once overwrote a stored "danger" verdict with "safe", because live analysis missed ingredients written in a foreign language. The rule is now strict. The stored health score sets a floor, and a scan can raise the alert level but never lower it.

```
Laptop (dashboard)                       Phone (scanner)
┌───────────────────────┐   WebRTC / SSE   ┌──────────────┐
│  Live result, map,    │◄────────────────►│  Camera,     │
│  alerts               │                  │  barcode     │
└───────────┬───────────┘                  └──────────────┘
            │
   Node.js / Express, single HTTPS port
            │
   MySQL  ·  open product data  ·  optional AI packaging check
```

## Stack

React 19 · Vite · Tailwind CSS · Framer Motion · PeerJS (WebRTC) · ZXing · Express · MySQL · Leaflet · PWA · mkcert (local HTTPS) · Gemini (optional)

---

*© Brayan Sorgho. All rights reserved. Presentation only: the code is not published here.*
