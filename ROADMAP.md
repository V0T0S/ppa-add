# Roadmap

## Near term

- [ ] **i18n**: localize all user-visible messages (see the plan below).
- [ ] **Tests**: a TAP-based test suite (`tests/*.t`, runner `tests/run.sh`)
      covering argument parsing, fingerprint extraction, suite validation
      and the remove globs — with stubbed `curl`/`gpg`/`sudo`.
- [ ] **Man page**: `ppa-add.1` with synopsis, examples and exit codes.
- [ ] **CI**: `shellcheck` + `bash -n` on every push; tests on Ubuntu LTS.

## i18n plan

Today every message is English, and the English strings are the source of
truth, written inline at the call sites. Localization will:

1. Route all messages through a single helper — `msg <key> <en-default>` —
   instead of raw `echo`/`die` calls.
2. Keep catalogues in `locale/<lang>/strings` files (`key = translation`,
   one line per key), owned by the project; no gettext runtime dependency.
3. Resolve the language via `PPA_ADD_LANG` → `LANG`/`LC_ALL` (`_`-suffix
   form), with the fallback chain: chosen language → `en` → built-in
   English default at the call site. A missing or partially translated
   catalogue must never be an error.
4. Freeze the key set once messages stabilize; keys are `MSG_<NAME>`,
   uppercase, stable across releases.

## Ideas / maybe

- `--src` / config option to also write `Types: deb deb-src`.
- `--keyring-dir` and `--sources-dir` overrides for chroot/container use.
- A `--list` mode: print available suites for a PPA without adding it.
- Support for non-Launchpad apt repositories with a similar
  fingerprint-pinned workflow.
- Dry-run mode (`--dry-run`) printing every planned write.
