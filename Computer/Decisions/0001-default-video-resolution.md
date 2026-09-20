# 0001 — Default Video Resolution

**Status:** Accepted

## Decision

The default framebuffer resolution of the Ashet Home Computer is **640×400 at 8 bits per pixel**.

## Rationale

At 8 bits per pixel, one pixel occupies one byte.

```
640 × 400 × 1 B = 256,000 B
```

The RP2350 contains 512 KiB of internal SRAM. Half of that memory is:

```
256 KiB = 262,144 B
```

A complete 640×400 framebuffer therefore requires **256,000 B**, leaving **6,144 B** before reaching the 256 KiB boundary.

640×400 is the largest standard resolution selected for the computer that fits a complete 8 bpp framebuffer into 256 KiB, i.e. half of the RP2350 internal SRAM.

## Consequences

- A complete default-resolution framebuffer can fit inside half of RP2350 SRAM.
- The default video mode uses 8 bpp [Ashet HSV](../../Platform/Ashet%20HSV.md) pixels.
- Higher resolutions at 8 bpp require more than the 256 KiB framebuffer budget.
