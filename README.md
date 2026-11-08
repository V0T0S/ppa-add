# ppa-add

**[Читать по-русски](README.ru.md)**

`ppa-add` is an alternative to `add-apt-repository` for adding and removing
[Launchpad PPAs](https://launchpad.net/) on Ubuntu and its derivatives.

## Why ppa-add?

If you have ever run into the situation where the standard recipe —
`sudo add-apt-repository ppa:someuser/somerepo` — simply does not work,
this script is for you. My friend
[M. Vibe](https://github.com/mistralai/mistral-vibe) and I wrote it in
pure Bash.

What is wrong with the standard tool? No idea. But:

- The PPA signing key is fetched through `dirmngr`, which can pin the CPU
  at 100% and then simply exit with an error.
- You learn that a key is missing far too late — from the `apt update`
  output.

`ppa-add` instead:

1. Fetches the PPA web page once and reads the signing key fingerprint and
   the list of suites from it (the Launchpad API is the fallback source for
   both).
2. Validates the chosen suite against the PPA's actual `InRelease` files —
   before anything is written to the system.
3. Downloads the key over HTTPS and checks that the fingerprint of the
   downloaded key matches the fingerprint published by Launchpad — a wrong
   or substituted key is never installed.
4. Writes a deb822 `.sources` file with `Signed-By:` pinning the key in
   `/etc/apt/keyrings/`, so the key is trusted by this PPA only — not by
   every repository on the system.

## Requirements

- Ubuntu or a derivative (Linux Mint, etc.) using apt
- bash 4+, `curl`, `gpg`, `sudo`

## Install

```bash
sudo install -Dm755 bin/ppa-add /usr/local/bin/ppa-add
```

Installation is optional: running `bash bin/ppa-add …` straight from the
project directory works as well.

## Usage

```
ppa-add [-r] [-n] <user/repo> [suite]

  -r, --remove     remove the PPA (sources file and signing key)
  -n, --no-update  do not run apt-get update afterwards
```

The `suite` defaults to your system's codename and resolves through
`DEBIAN_CODENAME` → `UBUNTU_CODENAME` → `VERSION_CODENAME` (from
`/etc/os-release`), so it works on Ubuntu and on derivatives whose own
codename differs from the Ubuntu suite (e.g. Linux Mint).

```bash
# Add a PPA for your system's codename
sudo ppa-add someuser/somerepo

# Add a PPA for a specific suite
sudo ppa-add someuser/somerepo jammy

# Add several PPAs, refresh the index once
sudo ppa-add -n user1/repo1
sudo ppa-add -n user2/repo2
sudo apt-get update

# Remove a PPA: its sources file(s) and signing key
# (installed packages are kept; use ppa-purge to downgrade first)
sudo ppa-add -r someuser/somerepo
```

If the PPA has no release for the requested suite, the tool lists the
available ones instead of writing anything:

```
ppa-add: no release 'noble'. Available: jammy focal
     (ppa-add someuser/somerepo <one of them>)
```

## Exit codes

- `0` — success
- `1` — network/validation error (nothing was written)
- `2` — usage error (unknown option or bad arguments)

## Roadmap

Planned work, including localization of the interface messages, is tracked
in [ROADMAP.md](ROADMAP.md).

## License

Apache-2.0. See [LICENSE](LICENSE) and [NOTICE](NOTICE); SPDX identifiers
are present in the sources.
