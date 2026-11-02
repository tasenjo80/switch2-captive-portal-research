# Switch 2 captive-portal WebKit research

Public, sanitized research notes on whether the built-in
`WifiWebAuthApplet` (captive-portal browser) can be used as a durable WebKit
entry on Switch 2, and how far a local-only captive-portal chain can go.

This repository is documentation only. It does **not** contain exploit code,
raw logs, heap addresses, object offsets, firmware, keys, game content, or
screenshots.

## Status

Verified on one Switch 2 on firmware **23.0.1**:

- The local captive-portal entry launches `WifiWebAuthApplet` and loads a
  controlled page. The applet re-opens without a reboot.
- A WebKit `CSSFontFace` use-after-free is reachable from the applet. The
  relevant public WebKit bugs were present in Nintendo's published 23.0.0 OSS
  WebKit source at the time of review.
- The freed object was reclaimed with same-size `ArrayBuffer`s, and a 4-byte
  arbitrary read plus a self-address chain were confirmed.
- The chain reaches a full **16 MB arbitrary read/write window** in the applet
  process. This was independently reproduced on hardware with a public
  community WebKit chain for the same applet/firmware; the chain is
  timing-sensitive and the renderer often dies after it completes
  (`2168-0002`). One run survived the cleanup.
- The applet's FS client, mount table, and session structures were mapped.
  `.bss` and the FS client page are writable; `.data` is read-only after init.
- A no-reclaim trigger crashes the console to error `2168-0002`; this is
  consistent with the applet sharing the host address space.

Not verified / currently blocked:

- A web-controlled FS path. The applet exposes no path API (`indexedDB` is
  null; cache names are fixed), and the FS request path is thread-local.
  The FS session/manager structures are self-referential; a simple fake
  object does not work.
- An applet-side PID1/FullPermission promotion. The permission registry is in
  the `fsp-srv` server/KIP process; applet ARW cannot reach it. A read-only
  probe matched the applet base and `fsp-srv` string but crashed on the
  server-relative registry address.
- The Chat/GameChat STUN path. GameChat requires NSO, persistent internet, a
  phone number, and the latest firmware; the offline/90DNS test console
  returned 2155-8007 and never reached TURN/STUN.
- Applet `fsp-srv` calls, the `fs` path-normalization route, kernel access,
  or CFW.
- The 24-hour gate test.
- Any claim that the captive portal is a permanent or untethered entry.

ARW is not a jailbreak. The UAF path is timing-sensitive and has crashed
repeatedly. See [`docs/status-2026-11-02.md`](docs/status-2026-11-02.md) and
[`docs/results-2026-11-01.md`](docs/results-2026-11-01.md).

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
  status-2026-11-02.md
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
- A public community WebKit chain for the same applet/firmware was used for
  the independent hardware reproduction of ARW. That repository is now
  offline; its code is not copied here, and the result is labeled
  independently reproduced, not audited.
- Community claims about the applet NPDM, `fsp-srv`, and the `fs` overflow are
  labeled unverified in the notes.

This is independent research. It is not affiliated with Nintendo. No
firmware, keys, tickets, game dumps, or piracy tools are provided or
supported.
