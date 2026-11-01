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
  -> stable arbitrary read/write (data-only; PAC/XOM/CFI block ROP)
  -> applet fsp-srv calls
  -> fs path-normalization route
  -> fs ARW / code execution
  -> kernel read/write
  -> CFW
```

Stages after the read primitive are not verified.

## What is verified

- Local entry and applet launch on 23.0.1.
- The UAF is reachable; no-reclaim crashes the system.
- A same-size reclaim, a 4-byte read, and a self-address chain in successful
  runs.

## Open questions

1. Is there a stable write/ARW path that does not depend on the fragile read
   chain?
2. Does the applet's NPDM actually grant `fsp-srv` at runtime, and which
   filesystem can it mount?
3. Is the `fs` `GetEntryType` path-normalization bug reachable from the
   applet's `fsp-srv` session?
4. Can the trigger/reclaim be made reliable enough for repeatable
   development?
5. Does the 24-hour gate block the applet, and can a local-only portal keep it
   open?
6. Is there a non-WebKit path from the applet's other services
   (`bsd:a`, `ns:web`, `ns:vm`, `caps:a`, ...) to a useful primitive?

## Next steps

- Improve trigger/reclaim reliability before attempting the write stage.
- Verify the applet NPDM/`fsp-srv` claim from a dump if one becomes available.
- Keep the console offline and do not update.
- Monitor public research releases; any released code is used privately and is
  not republished here.
