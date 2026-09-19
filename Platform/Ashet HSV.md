# Ashet HSV

Ashet HSV is the platform's 8-bit **computable color space** for representing a practical, deterministic set of 256 colors.

It is not an arbitrarily selected 256-color palette. Each byte encodes structured hue, value, and saturation information, so the encoded color index can be computed directly from quantized color components and decoded back without searching a palette.

It is based on HSV-style components, but uses a modified encoding to avoid wasting large parts of the 8-bit space on duplicate blacks and grays.

## Bit Layout

An Ashet HSV color is one byte:

| Bits | Field | Width |
| ---: | --- | ---: |
| 0..2 | Hue | 3 bits |
| 3..5 | Value | 3 bits |
| 6..7 | Saturation | 2 bits |

Equivalently:

```text
bit 7                         bit 0
+--------+-----------+-------------+
| Sat[1:0] | Value[2:0] | Hue[2:0] |
+--------+-----------+-------------+
```

The encoded byte value is:

```text
color = hue | (value << 3) | (saturation << 6)
```

## Computable Encoding

For non-gray colors, the encoded byte is computed directly:

```text
color = hue | (value << 3) | (saturation << 6)
```

For grayscale colors, saturation is zero and the lower six bits directly encode grayscale brightness:

```text
color = gray
```

where `gray` is in the range `0..63`.

This means software does not need to maintain a hand-authored 256-entry palette and perform a linear search to find the closest palette entry.

Instead, a color can be quantized into Ashet HSV components and its byte value calculated directly. Likewise, the hue, value, saturation, or grayscale level can be recovered from the byte using masks and shifts.

A lookup table may still be used for fast conversion from Ashet HSV to a hardware-specific output format, but such a table is an implementation optimization rather than the definition of the color space.

## Hue

For non-gray colors, the 3-bit hue field selects one of eight hues in 45° steps:

| Hue | Angle | Color |
| ---: | ---: | --- |
| 0 | 0° | Red |
| 1 | 45° | Yellow |
| 2 | 90° | Lime |
| 3 | 135° | Green |
| 4 | 180° | Cyan |
| 5 | 225° | Blue |
| 6 | 270° | Purple |
| 7 | 315° | Magenta |

Hue has no special interpretation when saturation is non-zero.

## Value

For non-gray colors, the 3-bit value field encodes eight non-black brightness levels.

Unlike ordinary HSV quantization, zero does **not** mean black.

| Value | Brightness |
| ---: | ---: |
| 0 | 12.5% |
| 1 | 25% |
| 2 | 37.5% |
| 3 | 50% |
| 4 | 62.5% |
| 5 | 75% |
| 6 | 87.5% |
| 7 | 100% |

This avoids producing 64 duplicate black encodings.

## Saturation

The 2-bit saturation field has four values:

| Saturation | Meaning | Saturation Level |
| ---: | --- | ---: |
| 0 | Gray encoding mode | 0% / grayscale |
| 1 | Low saturation | 33% |
| 2 | Medium saturation | 66% |
| 3 | Fully saturated | 100% |

## Gray Encoding

When `saturation == 0`, the hue and value fields are not interpreted as hue and value.

Instead, their six bits are combined into one 6-bit grayscale brightness value:

```text
gray = hue | (value << 3)
```

This provides 64 distinct gray levels from black to white:

| Gray value | Result |
| ---: | --- |
| 0 | Black |
| 1..62 | Intermediate gray levels |
| 63 | White |

Because saturation occupies the upper two bits, every gray value is encoded in the range:

```text
0x00..0x3F
```

This also means a simple unsigned comparison against `0x40` can distinguish grayscale colors from non-gray colors.

## Color-Space Properties

The encoding provides:

- 64 distinct gray levels from black to white
- 8 hues
- 3 non-zero saturation levels for each hue
- 8 brightness levels for each non-gray color
- exactly 256 distinct encodings

Black is encoded as `0x00`.

White is encoded as `0x3F`; it is intentionally not `0xFF`.

## Named Colors

The ABI defines the following canonical named colors:

| Name | Hue | Value | Saturation | Encoded Value |
| --- | ---: | ---: | ---: | ---: |
| Black | 0 | 0 | 0 | `0x00` |
| White | 7 | 7 | 0 | `0x3F` |
| Red | 0 | 7 | 3 | `0xF8` |
| Yellow | 1 | 7 | 3 | `0xF9` |
| Lime | 2 | 7 | 3 | `0xFA` |
| Green | 3 | 7 | 3 | `0xFB` |
| Cyan | 4 | 7 | 3 | `0xFC` |
| Blue | 5 | 7 | 3 | `0xFD` |
| Purple | 6 | 7 | 3 | `0xFE` |
| Magenta | 7 | 7 | 3 | `0xFF` |

## Rationale

A direct 3-bit hue, 3-bit value, 2-bit saturation mapping would waste many encodings:

- `value == 0` would produce 64 equivalent black values
- `saturation == 0` would duplicate each gray level across all eight hue values

Ashet HSV avoids both cases:

- non-gray values map value codes 0..7 to brightness levels 1..8
- saturation zero switches to a dedicated 6-bit grayscale encoding

This yields a useful color for every possible byte value while keeping the mapping algorithmic. The encoded value can be computed directly instead of locating a color through a linear search over an arbitrary palette.

## Source

This specification is derived from the Ashet OS ABI definition at commit:

`fe3d78bc086899e5ba063807e40d6c4ac738b25d`

specifically the `Color : u8` definition in `src/abi/src/ashet.abi`.

The 32-bit color formats declared alongside `Color` are intentionally outside the scope of this specification.
