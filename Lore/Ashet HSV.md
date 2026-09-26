# Ashet HSV — Design History

Ashet HSV did not start as a deliberately unusual color space. It grew out of repeated attempts to find an 8-bit color representation that was compact, visually useful, and still cheap to work with in software.

This document records that design history. The normative encoding is specified in [Platform/Ashet HSV.md](../Platform/Ashet%20HSV.md), and the platform decision selecting Ashet HSV is recorded in [Platform/Decisions/0009-ashet-hsv-platform-color-format.md](../Platform/Decisions/0009-ashet-hsv-platform-color-format.md).

## Direct RGB

An early version of the system used RGB333. It always had a visually strange character, and the later 8-bit format therefore moved to a direct RGB encoding.

The first 8-bit candidate that was actually selected was **RGB233**. RGB233 is already somewhat unusual; RGB332 is the much more common arrangement.

Before choosing it, several direct RGB layouts were compared:

- RGB332
- RGB323
- RGB233

A comparison tool was built around the 640×480 reference-image set from [testimages.org](https://testimages.org/).

For every tested color format or palette, the tool generated an HTML page containing one table row per reference image. The columns showed:

- the original image
- conversion to the closest available color
- positionally dithered conversion
- Floyd–Steinberg dithered conversion

This made it possible to open one generated page and quickly get a broad visual impression of how a palette behaved across many kinds of images.

Of RGB332, RGB323, and RGB233, **RGB233 gave the best overall visual impression**, but it still was not satisfactory:

- images tended to look slightly blue-tinted
- there were no true neutral grays
- the result generally looked visibly quantized and "off"

RGB233 was nevertheless selected because it looked better than the other direct 8-bit RGB arrangements tested.

RGB222 was also evaluated because equal channel widths provide true grays. The cost was only four brightness levels per channel, and the resulting images looked substantially worse.

## Looking Beyond RGB

After roughly half a year of using the direct-RGB result, its limitations were annoying enough to justify revisiting the color format.

Several alternatives were evaluated.

One was the classic web color palette: a 6×6×6 RGB cube. Compared with two-bit RGB channels, six levels per channel give noticeably finer brightness variation.

Predetermined 256-color palettes from [Lospec](https://lospec.com/palette-list) were also explored and often gave good visual results.

However, there was an important design requirement: the palette should be **computable**.

Given a quantized color, software should be able to calculate its 8-bit index directly. It should not need to search an arbitrary 256-entry palette for the closest match.

That requirement ruled out most hand-designed palettes, including many of the visually strongest candidates.

The next step was to stop assuming the encoded components had to be RGB at all.

Several other color spaces were explored, including:

- YUV
- HSV
- HSL

Different bit allocations were tested using the same image-comparison workflow.

Of these experiments, **HSV323** gave the best visual results.

## The Waste in Naive HSV323

A direct 3-bit hue, 2-bit saturation, 3-bit value encoding has an attractive structure:

- 8 hue levels
- 4 saturation levels
- 8 value levels

But ordinary HSV contains degeneracies that waste encoding space.

When saturation is zero, hue is irrelevant. A gray therefore appears once for every encoded hue.

When value is zero, hue and saturation are both irrelevant. Black therefore appears under many different byte values.

For an 8-bit indexed format, spending a significant part of the 256 possible values on duplicate colors is particularly unattractive.

The first important optimization was therefore to change the meaning of the remaining bits when saturation is zero.

## Saturation Zero Becomes Grayscale

Instead of ignoring the hue bits when saturation is zero, the hue and value fields are joined together.

The six lower bits then directly encode grayscale brightness:

```text
gray = hue | (value << 3)
```

This turns the saturation-zero range into a dedicated 64-level grayscale ramp.

Instead of eight grayscale brightness levels repeated across hue values, Ashet HSV gets **64 distinct neutral grays**, including black and white.

This was a major improvement both in visual quality and in use of the encoding space.

## Removing Duplicate Black

One source of duplication still remained for colored values.

In ordinary HSV, value zero means black regardless of hue or saturation. Keeping zero as one of the three-bit value levels would therefore still spend many non-gray encodings on the same color.

Ashet HSV avoids this by making the encoded non-gray value field represent **1 through 8**, rather than 0 through 7.

The stored three-bit value is therefore interpreted as:

```text
HSV value = (value_bits + 1) / 8
```

The lowest colored brightness is 12.5%, and the highest is 100%.

Black exists only in the dedicated grayscale range.

## The Result

The final format has:

- 8 hues
- 3 non-zero saturation levels
- 8 non-black value levels for colored entries
- 64 dedicated grayscale levels

Together, these occupy all 256 byte values with no intentionally duplicated palette entries.

Conceptually, the format provides nine useful brightness levels when black is counted together with the eight non-black colored levels, four saturation states including grayscale, eight hue positions, and a much finer 64-level neutral axis.

When evaluated using the same reference-image workflow, this format gave consistently strong results.

It also preserved the properties that were important for the system:

- the palette index is computable directly
- brightness can be increased or decreased through the encoded value field
- saturation can be adjusted through the encoded saturation field
- grayscale has much higher resolution than small direct-RGB formats
- every one of the 256 possible byte values has a useful meaning
- representative test images retained good visual fidelity for an 8-bit format

That modified HSV323 encoding became **Ashet HSV**.
