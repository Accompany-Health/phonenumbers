# Architecture

## Overview

`phonenumbers` is a Go port of Google's [libphonenumber](https://github.com/google/libphonenumber) library. It is Accompany Health's fork of [nyaruka/phonenumbers](https://github.com/nyaruka/phonenumbers), maintained to stay current with upstream metadata and used across backend services for phone number parsing, formatting, and validation.

The library has no network or database dependencies — it is a pure Go library that embeds all territory metadata at compile time.

---

## Package Structure

```
phonenumbers/
├── phonenumbers.go          # Core public API
├── phonenumbers_test.go     # Comprehensive test suite
├── metadata.go              # go:embed directives wiring data/ files into the binary
├── shortnumber_info.go      # Short number (emergency/info service) validation
├── shortnumber_info_test.go
├── matcher.go               # Phone number matching logic
├── builder.go               # String/buffer building utilities
├── insertablebuffer.go      # Custom byte buffer
├── serialize.go             # Protobuf serialization/deserialization
├── metadata_util.go         # Metadata helpers
├── phonenumber.pb.go        # Generated protobuf — PhoneNumber message
├── phonemetadata.pb.go      # Generated protobuf — PhoneMetadata message
├── data/                    # Embedded metadata files (gzipped XML/text)
│   ├── metadata.xml.gz              # Territory formatting/validation rules
│   ├── shortnumber_metadata.xml.gz  # Short number metadata
│   ├── countrycode_to_region.xml.gz # Country code → region mappings
│   ├── prefix_to_timezone.xml.gz    # Number prefix → timezone
│   ├── prefix_to_carriers/          # Number prefix → carrier (per locale)
│   └── prefix_to_geocodings/        # Number prefix → city/region (per locale)
└── cmd/
    ├── buildmetadata/       # CLI: regenerates data/ from upstream XML
    └── phoneparser/         # CLI: example phone number parser
```

---

## Public API

The root `phonenumbers` package exposes all public functions. Key functions:

| Function | Description |
|---|---|
| `Parse(number, defaultRegion)` | Parse a phone number string into a `PhoneNumber` |
| `Format(number, format)` | Format a `PhoneNumber` in E164/INTERNATIONAL/NATIONAL/RFC3966 |
| `IsValidNumber(number)` | Check if a number is valid globally |
| `IsValidNumberForRegion(number, region)` | Check validity for a specific region |
| `IsPossibleNumber(number)` | Check if the format is possible (lighter than IsValid) |
| `GetNumberType(number)` | Returns FIXED_LINE, MOBILE, TOLL_FREE, VOIP, etc. |
| `GetRegionCodeForNumber(number)` | Determine the region from a parsed number |
| `GetNationalSignificantNumber(number)` | Extract the national number portion |
| `GetExampleNumber(regionCode)` | Return a valid example number for a region |
| `NewShortNumberInfo()` | Returns a `ShortNumberInfo` for emergency/short number validation |

### Phone Number Formats

| Format | Example |
|---|---|
| `E164` | `+16502530000` |
| `INTERNATIONAL` | `+1 650-253-0000` |
| `NATIONAL` | `(650) 253-0000` |
| `RFC3966` | `tel:+16502530000` |

---

## Data Architecture

Territory metadata is embedded at compile time using Go's `//go:embed` directive (`metadata.go`). The files in `data/` are gzipped XML and text files sourced directly from Google's libphonenumber repository — no intermediate binary conversion step. This means:

- **No file I/O at runtime** — metadata is compiled into the binary via `go:embed`
- **Lazy loading with caching** — metadata for each territory is deserialized on first use and cached in memory
- **~230 territories supported** — formatting rules, validation patterns, carrier/geocoding/timezone maps

The `PhoneNumber` and `PhoneMetadata` types are defined as Protocol Buffer messages (`.proto` → `.pb.go`), providing efficient serialization.

---

## Metadata Update Flow

When Google releases new libphonenumber metadata, the `buildmetadata` command regenerates all files in `data/`:

```
go install github.com/Accompany-Health/phonenumbers/cmd/buildmetadata
buildmetadata
```

Internally it:
1. Fetches the upstream Google libphonenumber repository
2. Copies and gzips the XML/text metadata files into `data/`

The gzipped files are committed to the repo and embedded into the binary at build time via `go:embed`.

---

## Design Principles

1. **Strict port** — the aim is to match Google's libphonenumber behavior exactly. No custom features are added beyond what exists upstream.
2. **Metadata fidelity** — always built against the latest upstream XML; the CHANGELOG records which libphonenumber metadata version each release targets.
3. **Production-proven** — used daily at scale for parsing and validation across ~230 territories.
4. **Zero infrastructure** — pure library with no runtime service dependencies.
