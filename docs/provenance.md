# Provenance and confidence

Reviewed: 2026-11-01.

## Primary sources

- **Nintendo Switch 2 OSS source**, public download
  (`NS2_23.0.0.zip`, 23.0.0 or later), WebKit `webkit_3.12.11` and
  `WKC_3.12.11`. Used for engine-side code facts: the `CSSFontFace` member
  layout, the raw-reference `FontFaceSet::load` path, the `iterateClients`
  behavior, and the WKC file-proc interface.
- **Public WebKit records**: bug 313577 / CVE-2026-43715 and bug 312202, plus
  the upstream fix commits. Used for the bug history and the trigger family.
- **Public PS4/PS5 reference**: `ntfargo/CSSFontFace-Exploit` (MIT). Used as a
  same-bug-family reference for the `m_featureSettings` read primitive and the
  general exploitation model. Its code is not copied here and it is not
  Switch code.

## Hardware observations

- One Switch 2, firmware 23.0.1, one region, one test network.
- The session logs are private. Only sanitized summaries are published.
- Exact heap addresses, offsets, spray counts, and timings are withheld.

## Community claims (unverified)

- The applet NPDM's `fsp-srv` permission and `fsPermissions` value.
- The applet's `nn::fs` client and `FileChooser` path function.
- The `fs` `GetEntryType` path-normalization overflow and its use for
  `fs`/kernel access.
- The community worker/FS harness and its offsets.

These are labeled unverified and are not used as confirmed facts.

## Confidence

- Engine code facts: high, from Nintendo's published source.
- Entry/UAF/reclaim/read/self-address: verified on one console.
- Write/ARW, `fsp-srv`, `fs`, kernel/CFW: not verified.
- Gate behavior: not tested beyond the 24-hour window.
