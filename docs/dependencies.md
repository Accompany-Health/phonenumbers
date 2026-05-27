# Dependencies

## Runtime Dependencies

This is a pure Go library with **no runtime infrastructure dependencies** (no database, no network calls, no external services at runtime).

---

## Go Module Dependencies

### Direct

| Module | Version | Purpose |
|---|---|---|
| `github.com/stretchr/testify` | v1.9.0 | Test assertions |
| `golang.org/x/exp` | v0.0.0-20240525044651 | Experimental utilities (slices, maps) |
| `golang.org/x/text` | v0.15.0 | Unicode text normalization |
| `google.golang.org/protobuf` | v1.34.1 | Protocol Buffer serialization for phone/metadata types |

### Indirect

| Module | Purpose |
|---|---|
| `github.com/davecgh/go-spew` | Testify dependency — deep value printing |
| `github.com/pmezard/go-difflib` | Testify dependency — diff output |
| `gopkg.in/yaml.v3` | Testify dependency — YAML marshaling |

---

## Upstream Dependencies

This repo sits in a two-level fork chain:

```
google/libphonenumber  →  nyaruka/phonenumbers  →  Accompany-Health/phonenumbers
      (Java, metadata)        (Go port)                  (this repo)
```

### nyaruka/phonenumbers — upstream Go codebase

- **Source**: `https://github.com/nyaruka/phonenumbers`
- **What we track**: Go implementation changes — bug fixes, new API additions, performance improvements, updated `.proto` definitions
- **How to sync**: merge upstream commits from nyaruka into this repo (see [local-development.md](./local-development.md))

### google/libphonenumber — upstream metadata source

- **Source**: `https://github.com/google/libphonenumber`
- **What we track**: Territory metadata XML files (formatting rules, valid number patterns, carrier/geo/timezone maps)
- **Consumed by**: the `cmd/buildmetadata` CLI tool (run at development time, not runtime)
- **Output**: gzip-compressed Protocol Buffer blobs embedded in `gen/*.go`
- **How to sync**: run `buildmetadata` and commit the regenerated `gen/` files

Both are **maintenance-time** dependencies only — neither is a runtime dependency.

---

## No Internal Dependencies

This library does not depend on any other Accompany Health internal packages. It is a standalone utility library.
