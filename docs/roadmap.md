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
  -> fs path-normalization route
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
- No web-controlled FS path was found. The FS request path is thread-local,
  and the session/manager structures are self-referential.

## Open questions

1. Is there any web-controlled FS path or parameter? `indexedDB` is null,
   cache names are fixed, and the download/file surfaces are blocked.
2. Can the FS client or session manager be faked safely? A simple object in
   the reclaim buffer is not a drop-in replacement; redirecting the session
   pointer has crashed `fsp-srv` asynchronously.
3. Does the applet NPDM actually grant `fsp-srv` at runtime, and which
   filesystem can it mount?
4. Is the `fs` `GetEntryType` path-normalization bug reachable from the
   applet's `fsp-srv` session?
5. Can the trigger/reclaim and ARW be made reliable enough for repeatable
   development?
6. Does the 24-hour gate block the applet, and can a local-only portal keep it
   open?
7. Is there a non-WebKit path from the applet's other services
   (`bsd:a`, `ns:web`, `ns:vm`, `caps:a`, ...) to a useful primitive?

## Next steps

- Pause blind FS probing. The remaining FS work needs a firmware/applet dump
  for static analysis of the FS client and path builder, a second bug that
  yields a controllable FS parameter, or a released working FS chain.
- Keep the ARW work for read-only mapping and validation, not repeated
  pointer-redirect experiments.
- Verify the applet NPDM/`fsp-srv` claim from a dump if one becomes available.
- Keep the console offline and do not update.
- Monitor public research releases; any released code is used privately and is
  not republished here.
