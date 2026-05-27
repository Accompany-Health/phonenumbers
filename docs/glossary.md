# Glossary

Terminology specific to the `phonenumbers` library.

---

**E.164**
The international phone number format standardized by the ITU-T. Numbers are prefixed with `+` and the country calling code, with no spaces or punctuation (e.g., `+16502530000`). This is the canonical storage format.

**INTERNATIONAL format**
A human-readable format that includes the `+` country code and uses spaces or dashes as separators (e.g., `+1 650-253-0000`).

**NATIONAL format**
A country-specific human-readable format without the country code prefix, following local conventions (e.g., `(650) 253-0000` for US numbers).

**RFC3966**
A URI format for telephone numbers as defined in RFC 3966 (e.g., `tel:+16502530000`). Used in HTML `href` attributes and SIP contexts.

**libphonenumber**
Google's reference implementation of international phone number parsing and formatting, originally written in Java. This library is a Go port of it.

**metadata**
Territory-specific rules encoding valid number lengths, formatting patterns, national prefixes, carrier codes, and geographic area mappings. Sourced from Google's libphonenumber XML files and embedded in `gen/` as compiled binary blobs.

**PhoneNumber**
The Protocol Buffer message type representing a parsed phone number. Fields include `CountryCode` (integer), `NationalNumber` (uint64), `Extension` (string), and optional fields for Italian leading zero, raw input, and carrier/preferred domestic formats.

**PhoneMetadata**
The Protocol Buffer message type encoding all formatting and validation rules for a single territory (e.g., `US`, `GB`, `DE`).

**ShortNumber**
A short numeric code used for emergency services (e.g., `911`, `112`) or information services (e.g., `411`). Validated separately via `ShortNumberInfo`.

**NNS (National Significant Number)**
The portion of a phone number that is dialed after the country calling code and national prefix. Extracted via `GetNationalSignificantNumber`.

**country calling code**
The numeric prefix that identifies a country in the E.164 system (e.g., `1` for US/Canada, `44` for UK, `91` for India).

**region code**
A two-letter ISO 3166-1 alpha-2 code identifying a territory (e.g., `US`, `GB`, `IN`). Used as the `defaultRegion` parameter when parsing numbers that may lack a country code prefix.

**number type**
The classification of a phone number's use: `FIXED_LINE`, `MOBILE`, `FIXED_LINE_OR_MOBILE`, `TOLL_FREE`, `PREMIUM_RATE`, `SHARED_COST`, `VOIP`, `PERSONAL_NUMBER`, `PAGER`, `UAN`, `VOICEMAIL`, or `UNKNOWN`.
