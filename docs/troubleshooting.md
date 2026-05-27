# Troubleshooting

## Unexpected Parsing or Formatting Results

**Symptom**: `Parse` or `Format` returns a result that doesn't match expectations.

**Steps**:
1. Verify the result against the [libphonenumber online demo](https://libphonenumber.appspot.com). If the demo gives the same unexpected result, the issue is in Google's metadata or Java implementation — not this repo.
2. If the demo gives the *correct* result, the bug is in the Go implementation. Check [nyaruka/phonenumbers issues](https://github.com/nyaruka/phonenumbers/issues) — as the upstream Go fork, they may have already fixed it or have an open issue.
3. If nyaruka has a fix, pull it in via the upstream sync process (see [local-development.md](./local-development.md)).
4. If the bug is not in nyaruka either, it may be a metadata issue — report to [google/libphonenumber](https://github.com/google/libphonenumber).
5. If none of the above, open an issue on this repo with the input number, region, and expected/actual output.

---

## Metadata Appears Out of Date

**Symptom**: A phone number that should be valid is rejected, or carrier/geo lookups return stale data.

**Steps**:
1. Check `CHANGELOG.md` for the metadata version this release was built against.
2. Compare against the latest release tag in [google/libphonenumber](https://github.com/google/libphonenumber).
3. If upstream has newer metadata, run the update:
   ```bash
   go install github.com/Accompany-Health/phonenumbers/cmd/buildmetadata
   $GOPATH/bin/buildmetadata
   ```
4. Commit the regenerated `gen/` files and open a PR.

---

## Test Failures After a Metadata Update

**Symptom**: Tests pass on `main` but fail after running `buildmetadata`.

**Steps**:
1. Diff the new `gen/` files against the previous ones to understand what changed.
2. Check if [nyaruka/phonenumbers](https://github.com/nyaruka/phonenumbers) has a corresponding update — there may be test fixtures that need updating alongside the metadata.
3. Run `go test -v ./...` to identify which specific test cases are failing and what the new vs. expected values are.

---

## Build Failures: Protobuf Errors

**Symptom**: Compilation errors referencing `google.golang.org/protobuf` types.

**Steps**:
1. Ensure your local `google.golang.org/protobuf` version matches the one in `go.mod` (currently `v1.34.1`).
2. Run `go mod tidy` to resolve any dependency drift.
3. If using a local replace directive pointing to a different protobuf version, remove it.

---

## `golangci-lint` Failures

**Symptom**: CI or local lint fails.

**Steps**:
1. Ensure your `golangci-lint` version matches what CI uses (check `.github/workflows/`).
2. Run `golangci-lint run --fix` to auto-fix formatting issues.
3. For new lint rules introduced in CI, check the lint config at the repo root.
