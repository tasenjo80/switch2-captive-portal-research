# Methodology

Sanitized summary. Exact trigger pages, heap-grooming code, object offsets, and
heap addresses are withheld.

## Objective

Determine whether the built-in `WifiWebAuthApplet` can serve as a durable
WebKit entry that is not tied to a delistable game, and characterize how far a
local-only captive-portal chain can go on Switch 2 firmware 23.0.1.

## Environment

- One Switch 2 on firmware 23.0.1.
- A separate test AP with no WAN, no client forwarding, and no upstream DNS.
- A PC on the test LAN serving a bounded DNS/HTTP entry and a local portal.
- No TLS interception and no traffic forwarding.
- The console is kept offline outside the test AP.

## Entry test

1. Connect the console to the isolated test SSID.
2. Let the console perform its connectivity probe.
3. The entry answers only a short allowlist of Nintendo connectivity-test
   names and redirects recognized HTTP probes to a local page.
4. Record the applet User-Agent, build strings, HTTP statuses, and timing.
5. Close and re-open the applet to check whether it relaunches without a
   reboot.

The entry is a research tool, not a production captive portal.

## Font environment probe

The WebKit trigger needs a `FontFace` source that resolves synchronously.

- Named local families were tested with `FontFace` and `load()`.
- All named families tried returned `NetworkError`.
- The generic `systemfont` family loaded synchronously and was used for the
  trigger.

This is an applet-specific observation; it may differ by region or build.

## UAF reachability

A public WebKit `CSSFontFace` use-after-free was exercised with a
`document.fonts.load` path and a re-entrant style invalidation.

- Without a reclaim, the console crashed to the system error screen
  `2168-0002`.
- With a same-size `ArrayBuffer` reclaim spray, the applet stayed alive and
  reported both the UAF `FontFace` and the reclaimed buffer.
- The reclaimed object was then used for a 4-byte read primitive and a
  self-address chain.

The public write-up deliberately omits the exact object size, offsets,
spray counts, and timing. The private research log keeps them.

## Reliability

The UAF path is timing-sensitive. Successful runs and crashes were both
observed with the same page. A crash is not a finding and is not treated as
progress. Sessions stop after repeated crashes or any boot/error loop.

## Stop conditions

- System update prompt.
- Request to connect to Nintendo services or accept a real portal.
- Repeated applet or system crashes, an error-code loop, or unusual heat.
- Accidental internet access on the test network.
- A firmware update announced during the session.

## Limitations

- One console, one firmware, one region, one test network.
- The 24-hour gate test is not complete.
- A page load and a userland read primitive do not prove `fsp-srv`, `fs`,
  kernel access, or CFW.
- Community claims about the applet NPDM and the `fs` bug are not
  independently verified here.
