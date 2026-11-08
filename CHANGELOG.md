# Changelog

All notable changes to this project are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.1.0] - 2026-11-07

### Added

- Initial public version: add and remove Launchpad PPAs without
  `add-apt-repository` and without a working keyserver connection.
- Signing key fingerprint is read from the PPA web page (Launchpad API
  fallback) and the key is fetched over plain HTTPS, bypassing `dirmngr`.
- Downloaded key fingerprint is verified against the one published by
  Launchpad before installation.
- Suite list is parsed from the PPA page and validated against real
  `InRelease` files before anything is written.
- deb822 `.sources` entries with `Signed-By:` pinning the key in
  `/etc/apt/keyrings/`.
- `-r/--remove` removes deb822 sources files and the signing key.
- `-n/--no-update` skips the final `apt-get update` for batch operations.
- Suite defaults resolve through `DEBIAN_CODENAME` → `UBUNTU_CODENAME` →
  `VERSION_CODENAME`, supporting Ubuntu derivatives (Linux Mint, etc.).
- Bilingual documentation: `README.md` (English) and `README.ru.md`
  (Russian).
