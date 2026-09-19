# Expansion Card EEPROM Format

This document specifies the Expansion Card EEPROM image derived from the current Ashet OS `expcard.zig` definition.

The EEPROM contains card metadata and, optionally, a Propeller 2 low-level expansion driver. The source also defines an icon block format, but that icon block is **not currently part of the active EEPROM image layout**.

## Binary Encoding

The implementation serializes the metadata structure in **little-endian** byte order.

Reserved bytes and reserved bits are defined as zero.

Fixed-size strings are null-padded byte arrays. Their logical value ends at the first zero byte, or at the end of the array if no zero byte is present. The source does not define a character encoding for these strings.

## EEPROM Image

The active EEPROM image is exactly **4096 bytes**:

| Address Range | Size | Contents |
| --- | ---: | --- |
| `0x0000..0x01FF` | 512 B | Metadata block |
| `0x0200..0x07FF` | 1536 B | Reserved, must be zero |
| `0x0800..0x0FFF` | 2048 B | Propeller 2 low-level driver firmware |

The source asserts:

- EEPROM image size: 4096 bytes
- metadata offset: `0x0000`
- firmware offset: `0x0800`
- metadata size: 512 bytes
- firmware size: 2048 bytes

The physical Expansion Card EEPROM may be larger than this image. Bytes beyond `0x0FFF` are not defined by this source.

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

## Icon Block Format

The source defines an `IconBlock` type with a total size of **2048 bytes**, but the field that would place this block into the EEPROM image is currently commented out.

Therefore, the following describes the defined icon binary format, **not an active EEPROM storage location**.

### Layout

| Offset | Size | Contents |
| ---: | ---: | --- |
| `0x0000` | 256 B | 16×16 pixel indices |
| `0x0100` | 576 B | 24×24 pixel indices |
| `0x0340` | 1024 B | 32×32 pixel indices |
| `0x0740` | 1 B | 16×16 icon configuration |
| `0x0741` | 1 B | 24×24 icon configuration |
| `0x0742` | 1 B | 32×32 icon configuration |
| `0x0743` | 189 B | 63-entry RGB palette |

Each pixel is one byte.

The source explicitly defines pixel value `0` as transparent. Its comment describing the exact meaning of values `1..63` is incomplete, so this specification does not add semantics beyond that.

### Icon Configuration

Each icon has a one-byte packed configuration:

| Bits | Field | Meaning |
| ---: | --- | --- |
| 0..5 | Color Count | Number of colors used by the icon |
| 6..7 | Reserved | Must be zero |

A color count of zero disables the icon.

### Palette

The shared palette contains **63 entries**.

Each palette entry is an 8-bit-per-channel sRGB triplet:

```
R, G, B
```

for a total of three bytes per entry.

## JSON Metadata Input

The source also defines a JSON representation used to construct a Metadata Block.

The accepted fields are:

| Field | Type | Required |
| --- | --- | --- |
| Vendor ID | `u32` | yes |
| Product ID | `u32` | yes |
| Properties | Properties | yes |
| Driver Interface | DriverInterface | yes |
| Driver Specific Data | string | no; defaults to empty |
| Vendor Name | string | yes |
| Product Name | string | yes |
| Serial Number | string | yes |

When converting this representation into the binary Metadata Block:

- unknown JSON fields are rejected
- duplicate fields are rejected
- strings that exceed their fixed field size are rejected
- the fixed Magic Number and Version come from the binary structure defaults
- reserved fields are zero-filled
- the CRC32 checksum is computed automatically

## Source

This specification is transposed from:

`Ashet-Technologies/Ashet-OS/src/userland/libs/expcard/src/expcard.zig`

Where this document and the implementation disagree, the discrepancy should be resolved explicitly rather than inferred.
