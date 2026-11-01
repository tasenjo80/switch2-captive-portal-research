# Safety rules

These rules apply to every session. They are conservative on purpose.

## Network

- Use a separate test AP with **no WAN**, no client forwarding, and no
  upstream DNS.
- Do not use the daily/home network.
- Do not accept a real captive-portal ToS or log in to Nintendo services.
- Do not open eShop, NSO, online games, or System Update.
- Keep the console offline outside the test AP.

## Console

- Prefer a dedicated test console. A failed test can trigger a normal nag, and
  a pending update can produce a supernag.
- Do not insert a game card during a session.
- Power off fully before changing network or SD-card state.
- Back up save data before risky work. There is no hardware recovery path
  available to this project.
- A system-level crash (`2168-0002` or similar) is not assumed harmless:
  repeated crashes can leave storage or save data in a bad state.

## Test discipline

- One mutation or one trigger variant per batch.
- Keep a valid control before and after a risky input.
- Stop after repeated crashes, an error-code loop, unusual heat, or a boot
  failure.
- Do not loop a crash; analyze the result before the next attempt.
- Keep raw logs and crash media private.

## Stop immediately if

- a system update prompt appears;
- the console asks to connect to Nintendo services or accept a real portal;
- the applet enters an error-code loop or the console crashes repeatedly;
- the console becomes unusually hot;
- the test network accidentally has internet access;
- a firmware update is announced during the session.
