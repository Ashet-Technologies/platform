# Expansion Card EEPROM Format

This document specifies the Expansion Card EEPROM image based on the Ashet OS `expcard.zig` definition plus the Platform decision that the defined icon block is part of the EEPROM image.

Every Expansion Card EEPROM contains card metadata and a Propeller 2 low-level expansion driver. Cards may additionally embed an Expansion Card icon block.

## Binary Encoding

The implementation serializes the metadata structure in **little-endian** byte order.

Reserved bytes and reserved bits are defined as zero.

Fixed-size strings are UTF-8 encoded, null-padded byte arrays. Their logical value ends at the first zero byte, or at the end of the array if no zero byte is present.

Per the global Platform string-encoding decision, strings are UTF-8 unless a specification explicitly states otherwise.

## EEPROM Image

The mandatory EEPROM image is exactly **4096 bytes**:

| Address Range | Size | Contents |
| --- | ---: | --- |
| `0x0000..0x01FF` | 512 B | Metadata block |
| `0x0200..0x07FF` | 1536 B | Reserved, must be zero |
| `0x0800..0x0FFF` | 2048 B | Propeller 2 low-level driver firmware |

Every Expansion Card therefore requires at least a **4 KiB EEPROM**.

If the card embeds icons, the EEPROM image is extended with the optional Icon Block:

| Address Range | Size | Contents |
| --- | ---: | --- |
| `0x1000..0x17FF` | 2048 B | Expansion Card icon block |

A card with embedded icons must use at least an **8 KiB EEPROM**. The icon block itself occupies only 2048 bytes; the larger EEPROM size is the required storage device capacity when icons are present.

Bytes `0x1800..0x1FFF` of an 8 KiB EEPROM are currently undefined by this specification.

The metadata and firmware offsets and sizes follow the current source definition. The icon block corresponds to enabling the already-defined `IconBlock` after the mandatory 4 KiB image.

## Metadata Block

The Metadata Block occupies the first 512 bytes of the EEPROM image.

| Offset | Size | Field | Type | Description |
| ---: | ---: | --- | --- | --- |
| `0x0000` | 4 | Magic Number | `u32` | Fixed value `0xFBCC31FF` |
| `0x0004` | 4 | Version | `u32` | Metadata format version. Currently `1` |
| `0x0008` | 8 | Reserved | bytes | Must be zero |
| `0x0010` | 4 | Vendor ID | `u32` | Vendor identifier |
| `0x0014` | 4 | Product ID | `u32` | Vendor-specific product identifier |
| `0x0018` | 4 | Properties | packed `u32` | Feature flags |
| `0x001C` | 4 | Driver Interface | `u32` enum | Driver-interface identifier |
| `0x0020` | 96 | Reserved | bytes | Must be zero |
| `0x0080` | 128 | Driver Specific Data | fixed string | Driver-specific payload |
| `0x0100` | 64 | Vendor Name | fixed string | Vendor name |
| `0x0140` | 64 | Product Name | fixed string | Product name |
| `0x0180` | 32 | Serial Number | fixed string | Card serial number |
| `0x01A0` | 92 | Reserved | bytes | Must be zero |
| `0x01FC` | 4 | CRC32 Checksum | `u32` | CRC-32 ISO/HDLC over bytes `0x0000..0x01FB` |

### Magic Number

The Magic Number is:

```
0xFBCC31FF
```

This value encodes the Propeller 2 instruction `JNPAT #-1`.

The source deliberately uses this instruction as protection against EEPROMs that are too small. Such EEPROMs may wrap a read from address `0x0800` back to address zero, causing the metadata block to be loaded as Propeller 2 code.

If that happens, the first word executes as an unusual endless loop instead of allowing the Cog to continue executing arbitrary metadata bytes as instructions.

### Version

The only metadata version currently defined is:

```
Version = 1
```

The source does not define compatibility behavior for other versions.

## Properties

`Properties` is a packed 32-bit value:

| Bit | Field | Meaning |
| ---: | --- | --- |
| 0 | Requires Audio | Card requires an Audio-capable slot |
| 1 | Requires Video | Card requires a Video-capable slot |
| 2 | Requires USB | Card requires USB |
| 3..15 | Reserved | Must be zero |
| 16 | Has Firmware | Card provides a firmware block |
| 17 | Has Icons | Card provides icons |
| 18..31 | Reserved | Must be zero |

`Has Firmware` and `Has Icons` default to false in the source definition.

## Driver Interface

`Driver Interface` is a 32-bit enumeration.

The only value currently assigned by the source is:

| Value | Name |
| ---: | --- |
| `0` | `none` |

The source permits additional numeric values, but does not define their semantics.

## CRC32 Checksum

The checksum field is located at `0x01FC`.

The checksum is calculated over the first **508 bytes** of the Metadata Block, excluding the checksum field itself.

The algorithm is **CRC-32 ISO/HDLC** with the parameters documented by the source:

- polynomial: `0x04C11DB7`
- initial value: `0xFFFFFFFF`
- reflect input: on
- reflect output: on
- invert output: on
- reversed: no

The resulting 32-bit checksum is stored in the `CRC32 Checksum` field.

## Firmware Block

The Firmware Block occupies:

```
0x0800..0x0FFF
```

and is exactly **2048 bytes**.

It contains the Propeller 2 low-level expansion driver. The driver is loaded into the Cog corresponding to the Expansion Card and forms the low-level interface between the host system and the card.

The default contents of this block are all zero.

## Optional Icon Block

If `Has Icons` is set, the Icon Block occupies:

```
0x1000..0x17FF
```

and is exactly **2048 bytes**.

Cards without embedded icons are not required to provide storage beyond the mandatory 4 KiB image.

Cards with embedded icons must use at least an 8 KiB EEPROM.

### Pixel Format

Each icon uses one byte per pixel.

Pixel values are encoded directly in the **Ashet HSV** 8-bit color format defined in [Ashet HSV.md](Ashet%20HSV.md).

There is no per-icon or shared palette in the EEPROM image.

### Layout

| Block Offset | EEPROM Address | Size | Contents |
| ---: | ---: | ---: | --- |
| `0x0000` | `0x1000` | 256 B | 16×16 8 bpp pixel data |
| `0x0100` | `0x1100` | 576 B | 24×24 8 bpp pixel data |
| `0x0340` | `0x1340` | 1024 B | 32×32 8 bpp pixel data |
| `0x0740` | `0x1740` | 2 B | 16×16 icon configuration |
| `0x0742` | `0x1742` | 2 B | 24×24 icon configuration |
| `0x0744` | `0x1744` | 2 B | 32×32 icon configuration |
| `0x0746..0x07FF` | `0x1746..0x17FF` | 186 B | Reserved for future use |

The reserved bytes are currently unused and should be written as zero.

### Icon Configuration

Each icon has a two-byte configuration structure:

```zig
config: packed struct(u8) {
    is_transparent: bool,
    _padding: u6,
    enabled: bool,
},
transparent: u8,
```

The first byte is packed least-significant-bit first:

| Bit | Field | Meaning |
| ---: | --- | --- |
| 0 | `is_transparent` | Enables transparent-pixel handling |
| 1..6 | Reserved | Must be zero |
| 7 | `enabled` | Enables this icon size |

The second byte, `transparent`, contains the 8-bit pixel value that is treated as transparent when `is_transparent` is set.

If `enabled` is clear, that icon size is not present.

## Source

This specification is transposed from:

`Ashet-Technologies/Ashet-OS/src/userland/libs/expcard/src/expcard.zig`

The Platform specification intentionally differs from the current implementation source in the optional Icon Block layout: it uses direct 8 bpp Ashet HSV pixels, per-icon transparency configuration, and no palette. Other discrepancies should be resolved explicitly rather than inferred.
