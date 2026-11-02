# Roadmap and open questions

This is a hypothesis map, not a claim that the chain is complete.

## Chain model

```text
captive portal
  -> WifiWebAuthApplet (WebKit 613.0, no JIT)
  -> CSSFontFace use-after-free
  -> same-size ArrayBuffer reclaim
  -> 4-byte arbitrary read
  -> self-address chain
  -> full 16 MB ARW (verified)
  -> FS path injection (blocked)
  -> applet fsp-srv calls
  -> fs path/cache race (public ns2jb description; unverified)
  -> fs ARW / code execution
  -> kernel read/write
  -> CFW
```

Stages after ARW are not verified.

## What is verified

- Local entry and applet launch on 23.0.1.
- The UAF is reachable; no-reclaim crashes the system.
- A same-size reclaim, a 4-byte read, and a self-address chain in successful
  runs.
- A full 16 MB arbitrary read/write window in the applet process, reproduced
  across multiple runs on one console. The chain is timing-sensitive and the
  renderer often dies after it completes; one run survived cleanup.
- The applet FS client, mount table, WKC file procs, and session structures
  were mapped. `.bss` and the FS client page are writable; `.data` is
  read-only after init.
- A persistent FS storage/session object and its I/O buffer were mapped. The
  storage-buffer and HTTP-cache marker probes were negative.
- No web-controlled FS path was found. The FS request path/offset is
  thread-local and only exists during an FS call; the applet's data-only ARW
  cannot patch it.

## Open questions

1. Is there any web-controlled FS path or parameter? `indexedDB` is null,
   cache names are fixed, and the download/file surfaces are blocked.
2. Can the FS client or session manager be faked safely? A simple object in
   the reclaim buffer is not a drop-in replacement; redirecting the session
   pointer has crashed `fsp-srv` asynchronously.
3. Does the applet NPDM actually grant `fsp-srv` at runtime, and which
   filesystem can it mount?
4. Is the `fs` path/cache race (signed cache header) reachable from the
   applet's `fsp-srv` session? The public ns2jb source describes a cache race,
   not a simple `GetEntryType` overflow.
5. Can the trigger/reclaim and ARW be made reliable enough for repeatable
   development?
6. Does the 24-hour gate block the applet, and can a local-only portal keep it
   open?
7. Is there a non-WebKit path from the applet's other services
   (`bsd:a`, `ns:web`, `ns:vm`, `caps:a`, ...) to a useful primitive?
8. Is there a client-side permission copy or a legitimate `fsp-srv` command
   that grants access? The server-side PID1 registry is outside the applet's
   ARW, so applet-side promotion cannot reach it.

## LDN path (parked)

- The local-wireless (ldn) path was evaluated separately on 2026-11-02. The
  advertisement is LDN version 4 with AES-GCM and is key-gated by Switch 2/Ounce
  keys and game session material.
- No keys or second console were available; replaying the console's own
  advertisement did not produce a visible room, likely because it is
  self-filtered.
- Public NPDM metadata gives `ldn` no FS permissions, so code execution there
  would not directly provide FS.
- Bounded malformed action-frame and beacon-IE tests produced no crash.
- Parked. It is not a substitute for the FS path.

## Next steps

- Pause blind FS probing. The remaining FS work needs a firmware/applet dump
  for static analysis of the FS client and path builder, a second bug that
  yields a controllable FS parameter, or a released working FS chain.
- The applet-only FS route is capped as of 2026-11-03: the storage-buffer and
  HTTP-cache probes were negative, and the request path/offset is thread-local.
  Pivot to a parser target (`capsrv`, `migration`) or a native entry. The
  applet/firmware dump is unavailable.
- Do not pursue applet-side PID1 promotion; the permission registry is in the
  `fsp-srv` server process, not the applet.
- Park the Chat/GameChat STUN path: GameChat requires NSO, online access, a
  phone number, and the latest firmware; the offline/90DNS test console
  returned 2155-8007 and never reached TURN/STUN.
- Keep the ARW work for read-only mapping and validation, not repeated
  pointer-redirect experiments.
- Verify the applet NPDM/`fsp-srv` claim from a dump if one becomes available.
- Leave the ldn path parked; do not blind-fuzz it further.
- Keep the console offline and do not update.
- Monitor public research releases; any released code is used privately and is
  not republished here.
