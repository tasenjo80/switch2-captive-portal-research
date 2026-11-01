# Publication policy

Public means **sanitized**, not raw.

## Publish

- Methodology, test matrix, and stop conditions.
- Sanitized results: firmware, applet launch result, error text/code,
  User-Agent/build strings, WebKit feature observations, lifetime/re-open
  behavior, and high-level chain status.
- Verified-versus-unverified labels.
- Source provenance and generic credits.

## Do not publish

- Exploit code, heap-grooming code, trigger pages, payloads, or exact object
  offsets and heap addresses.
- Raw event logs, full client IPs, MAC addresses, SSIDs, passwords, console
  serials, account identifiers, or personal paths.
- Screenshots.
- NPDM/NCA/NSP/XCI/NRO/NSO files or any other console binary.
- Discord exports, private messages, invite links, or raw research media.
- Third-party code without a compatible license.

## Redaction checklist

1. Remove peer addresses, peer hashes, and any full IP or MAC.
2. Remove exact heap addresses, object offsets, spray counts, and timing
   details that would function as exploit instructions.
3. Keep only build-level User-Agent strings; drop anything unique.
4. Check the text for account, locale, timezone, or device identifiers.
5. Remove absolute local paths.
6. State the console count and firmware; do not name the owner.
7. Keep a limitations section: one console, one firmware, community claims not
   independently verified.

## Corrections and removal

If a claim is wrong, correct it in a dated note rather than silently editing
history. If a data-removal request arrives, remove the relevant material and
history if requested.
