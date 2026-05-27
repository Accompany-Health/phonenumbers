# Local Development

## Prerequisites

- **Go 1.23+** — [Install Go](https://go.dev/doc/install)
- **Git**
- No database, Docker, or other infrastructure required — this is a pure Go library.

---

## Setup

```bash
git clone https://github.com/Accompany-Health/phonenumbers.git
cd phonenumbers
go mod download
```

---

## Testing

```bash
# Run all tests
go test ./...

# Run with race detection (recommended before PRs)
go test -race ./...

# Run short tests only (skips slow tests)
go test -short ./...

# Run with coverage report
go test ./... -coverprofile=coverage.out -covermode=atomic
go tool cover -html=coverage.out
```

---

## Linting

```bash
golangci-lint run
```

---

## Building

```bash
go build ./...
```

---

## Syncing with Upstream (nyaruka)

This repo is a fork of [nyaruka/phonenumbers](https://github.com/nyaruka/phonenumbers). When nyaruka ships Go implementation changes (bug fixes, new APIs, `.proto` updates), pull them in:

```bash
# Add nyaruka as a remote (first time only)
git remote add nyaruka https://github.com/nyaruka/phonenumbers.git

# Fetch and merge upstream changes
git fetch nyaruka
git merge nyaruka/main
```

Resolve any conflicts, run `go test ./...`, and open a PR. Check nyaruka's CHANGELOG to summarize what changed.

---

## Updating Metadata

When Google releases a new version of libphonenumber, regenerate the metadata blobs:

```bash
go install github.com/Accompany-Health/phonenumbers/cmd/buildmetadata
$GOPATH/bin/buildmetadata
```

This clones the upstream repo, re-parses the XML, and overwrites the files in `gen/`. Commit the result and update `CHANGELOG.md` with the upstream metadata version.

---

## Using in Another Service

```bash
go get github.com/Accompany-Health/phonenumbers
```

```go
import "github.com/Accompany-Health/phonenumbers"

num, err := phonenumbers.Parse("6502530000", "US")
formatted := phonenumbers.Format(num, phonenumbers.E164)  // "+16502530000"
```

---

## Releasing

1. Update `CHANGELOG.md` with the new version and what changed.
2. Tag the release: `git tag v1.x.y && git push origin v1.x.y`
3. The `.goreleaser.yml` config handles GitHub release creation via CI.
