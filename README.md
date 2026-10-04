# NEXUS·IP — IP & Leak Analyzer

A single-file, fully client-side dashboard that shows your exact public IP, plots it
on a live satellite/street/topo map, and runs three independent leak probes.

**Live:** https://peymanph.github.io/myip/

## What it measures

| Section | Data |
|---|---|
| Public IP | resolved from `ipwho.is`, cross-checked against `ipinfo.io` and `ipv4.icanhazip.com` — disagreement between sources is reported as a finding |
| Location | city / region / country / postal, ASN, ISP, network type, coordinates with a realistic accuracy circle (city-level, never street) |
| Time | IP timezone + offset vs browser timezone + offset, live clock |
| Browser exposure | platform, languages, cores, GPU renderer, WebRTC availability, headless detection |
| Exposure score | 100 → drops only on observed findings (`−25` leak, `−15` warning); inconclusive probes are never counted as clean |

## Leak lab

1. **WebRTC ICE** — a real `RTCPeerConnection` collects STUN candidates for ~3 s.
   Any reflexive address that differs from the HTTP IP is a WebRTC leak; mDNS host
   names are reported as protected.
2. **Dual-stack** — IPv4-only and IPv6-only echo endpoints in parallel. A v4/v6
   geolocation or ASN split means the tunnel only carries one family.
3. **Timezone & locale** — `Intl` timezone, UTC offset and language region compared
   against the timezone registered for your IP.

## Design notes

- **No backend, no keys, no logging.** Every call is made by your browser straight to
  public CORS-enabled resolvers. Verify it in the network tab.
- Public resolvers rate-limit per source IP, so the primary source falls back to
  `ipinfo.io` and the page shows a chip per source (`ok` / `rate-limited` / `error`).
- Map layers: Esri World Imagery, Esri World Street Map, OpenTopoMap — all keyless,
  with attribution rendered in-page.

## Run locally

```sh
python3 -m http.server 8100 --directory .
# open http://127.0.0.1:8100/
```

Lookup a specific address with `?ip=1.1.1.1`.

MIT licensed. Map data © Esri, © OpenStreetMap contributors.
