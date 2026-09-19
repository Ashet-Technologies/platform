# 0008 — UTF-8 Is the Default String Encoding

**Status:** Accepted

## Decision

All strings defined by the Ashet Platform are encoded as **UTF-8** unless a specification explicitly states otherwise.

This rule applies to fixed-size strings, null-terminated strings, length-prefixed strings, textual metadata, and other string fields defined by Platform specifications.

## Rationale

Using one default encoding avoids repeating the encoding requirement for every string field and prevents ambiguity between implementations.

UTF-8 provides a compact representation for ASCII-compatible text while supporting the full Unicode character set.

## Consequences

- Platform specifications may omit an explicit encoding when the field uses UTF-8.
- Any field using a different encoding must state that encoding explicitly.
- Binary fields and opaque byte arrays are not strings and are unaffected by this decision.
