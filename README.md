# Switch 2 captive-portal WebKit research

Public, sanitized research notes on whether the built-in
`WifiWebAuthApplet` (captive-portal browser) can be used as a durable WebKit
entry on Switch 2, and how far a local-only captive-portal chain can go.

This repository is documentation only. It does **not** contain exploit code,
raw logs, heap addresses, object offsets, firmware, keys, game content, or
screenshots.

## Status

Partial chain, verified on one Switch 2 on firmware **23.0.1**:

- The local captive-portal entry launches `WifiWebAuthApplet` and loads a
  controlled page. The applet re-opens without a reboot.
- A WebKit `CSSFontFace` use-after-free is reachable from the applet. The
  relevant public WebKit bugs were present in Nintendo's published 23.0.0 OSS
  WebKit source at the time of review.
- The freed object was reclaimed with same-size `ArrayBuffer`s, and a 4-byte
  arbitrary read plus a self-address chain were confirmed.
- A no-reclaim trigger crashes the console to error `2168-0002`; this is
  consistent with the applet sharing the host address space.

Not verified:

- A stable arbitrary write / ARW. A fake CSSOM write path is close but the
  write itself is not yet confirmed.
- Applet `fsp-srv` calls, the `fs` path-normalization route, kernel access,
  or CFW.
- The 24-hour gate test.
- Any claim that the captive portal is a permanent or untethered entry.

The chain is timing-sensitive and the UAF path has crashed repeatedly. Do not
treat any stage as reliable.

## Safety

Use a dedicated test console and an isolated test network with no WAN. Do not
update, open eShop/NSO/online games, or insert a game card during a session.
Read [`docs/safety.md`](docs/safety.md) before running anything.

## Public / private boundary

Published here:

- methodology and test matrix;
- sanitized results, firmware, applet User-Agent/build strings, and feature
  observations;
- verified-versus-unverified labels;
- source provenance.

Withheld:

- exploit code, heap grooming, trigger pages, and payloads;
- raw event logs, full IPs, MAC addresses, SSIDs, console serials, account
  identifiers, or personal paths;
- screenshots;
- NPDM/NCA/NSP/XCI/NRO/NSO files or any other console binary;
- Discord exports, private messages, or invite links.

See [`docs/publication-policy.md`](docs/publication-policy.md).

## Layout

```text
README.md
docs/
  methodology.md
  results-2026-11-01.md
  roadmap.md
  safety.md
  publication-policy.md
  provenance.md
LICENSE
LICENSE-DOCS.md
CREDITS.md
CONTRIBUTING.md
SECURITY.md
```

## Sources and confidence

- Nintendo's public Switch 2 OSS source (`NS2_23.0.0.zip`, WebKit 613.0
  branch) is the primary source for the engine-side code facts.
- Public WebKit bug/CVE records and the upstream fix commits are used for the
  bug history.
- The public `ntfargo/CSSFontFace-Exploit` (MIT) is a PS4/PS5 reference for
  the same bug family; it is not Switch code and is not copied here.
- Community claims about the applet NPDM, `fsp-srv`, and the `fs` overflow are
  labeled unverified in the notes.

This is independent research. It is not affiliated with Nintendo. No
firmware, keys, tickets, game dumps, or piracy tools are provided or
supported.
