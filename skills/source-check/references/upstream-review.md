# Upstream Review

For any change that pulls in a new or updated upstream source: a dependency bump, an Alpine `aports` package version bump, a `flake.lock` update, or vendoring code from elsewhere.

## Dependency & license provenance

- **Origin check**: does the source URL still point where it always has? Watch for domain changes, repo transfers to unfamiliar maintainers/orgs, or a package name that's been re-registered (typosquatting risk on npm/crates/pypi-adjacent registries).
- **License diff**: compare the license in this version against the last one you reviewed. A license change (e.g. MIT → BUSL, or adding a "no commercial use" clause) is a decision point, not a rubber stamp — surface it, don't auto-approve it.
- **Changelog/release notes review**: read what actually changed, not just the version number. Look for anything security-relevant (auth changes, new network calls, new postinstall scripts) or behavior-breaking.
- **Maintainer/activity signal**: for smaller dependencies, a sudden maintainer handoff or a long-dormant package suddenly publishing a release is worth a second look, not necessarily a blocker.

## Release integrity

- **Checksums**: verify the published checksum (sha256/sha512) against what you compute locally from the downloaded artifact — don't trust a checksum that's hosted next to the artifact on the same unauthenticated channel without cross-referencing a second source (release notes, git tag, mirror).
- **Signatures**: if upstream signs releases (GPG, `signify`, `minisign`, Sigstore/cosign), verify the signature, don't just note that one exists.
- **Tag/commit correspondence**: for git-based sources, confirm the release tarball actually corresponds to the tagged commit (diff the tarball contents against `git archive` of the tag when in doubt).

## Alpine `aports` / `abuild` specifics

- Confirm `pkgver`/`pkgrel` bump follows Alpine's rules (`pkgrel` resets to 0 on a `pkgver` bump).
- Regenerate checksums with `abuild checksum` rather than hand-editing `sha512sums`/`sha256sums` in the `APKBUILD`.
- Run `abuild -r` (or the lint tooling available in your `aports` checkout) before pushing, and check for any new `CVE`/security advisory tied to the version being bumped to.
- Double check `subpackages`, `depends`, and `makedepends` still match what the new version actually needs — upstream sometimes adds a new build or runtime dependency silently.

## Nix flake specifics

- Read the `flake.lock` diff, not just "it updated" — check which inputs actually moved and whether any input's source URL or owner changed.
- Spot-check that the new `narHash` corresponds to the commit/tag it claims to, especially for inputs not pinned to a tagged release.
- For `nixops-config`, rebuild locally (`nix flake check` / a scoped `nixos-rebuild build`) before committing a flake update — don't commit an update you haven't built.
