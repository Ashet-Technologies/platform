# 0009 — Ashet HSV as the Platform Color Format

**Status:** Accepted

## Decision

Use **Ashet HSV** throughout the Ashet Platform as the standard 8-bit color representation unless a specification explicitly requires another format.

The normative encoding is specified in [Ashet HSV.md](../Ashet%20HSV.md). Its development history and palette experiments are documented separately in [Lore/Ashet HSV.md](../../Lore/Ashet%20HSV.md).

## Rationale

Ashet HSV provides a surprisingly effective representation for many different color-use cases while fitting every color into a single byte.

A key property is that Ashet HSV is a **computable color space**, not an arbitrarily chosen 256-color palette. Hue, brightness, and saturation are encoded structurally in the byte, so an Ashet HSV color value can be calculated directly from its quantized components. Software does not need to linearly search a palette to determine the corresponding 8-bit color index.

Using an 8-bit color format has several platform-level advantages:

- colors are compact and easy to reason about
- color data uses less bandwidth than 16-, 24-, or 32-bit formats
- the 8-bit color index can be computed directly instead of found by searching an arbitrary palette
- every possible color value can also be used directly as an index into a 256-entry hardware-conversion palette or lookup table
- the packed hue, brightness, and saturation fields allow inexpensive color manipulation

In particular, simple shading operations can be implemented by mutating the packed brightness and saturation fields, for example by incrementing or decrementing them with masking/clamping as appropriate.

The format also retains useful grayscale resolution while avoiding the duplicated black and gray values produced by a naive small HSV encoding.

## Consequences

- Platform interfaces that need an 8-bit color value use Ashet HSV by default.
- Framebuffers, icons, palettes, and other platform-defined color data may share the same canonical 8-bit representation.
- Implementations do not need a palette search to encode Ashet HSV colors; the index is derived directly from the color components.
- Implementations can optionally use a 256-entry lookup table for fast conversion from Ashet HSV to hardware-specific color formats.
- Simple brightness and saturation adjustments can often be performed directly on the encoded byte without converting through a larger color representation.
- Specifications that require a different color format must state that explicitly.
