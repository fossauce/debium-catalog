<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/fossauce/debium-catalog/main/.github/logo/debium-catalog-wordmark-dark.svg">
    <img alt="debium-catalog" src="https://raw.githubusercontent.com/fossauce/debium-catalog/main/.github/logo/debium-catalog-wordmark.svg" height="62">
  </picture>
</p>

<p align="center">
  Package definitions for debium.<br>
  Not part of Debian.
</p>

<p align="center">
  <a href="LICENSE"><img alt="License" src="https://img.shields.io/github/license/fossauce/debium-catalog"></a>
</p>

## Layout

| Path | Contents |
|---|---|
| `format` | format version debium checks before reading |
| `packages/<name>/package.yaml` | one definition |
| `packages/<name>/files/` | files a definition installs as they are |

## Kinds

| Kind | debium |
|---|---|
| `repack` | packs files upstream already built |
| `source` | compiles upstream source on the machine, as the user |
| `deb` | installs a vendor `.deb` unchanged, after checks |
| `meta` | depends on Debian packages; may add Debian components, backports and pins |
| `apt` | adds a vendor repository, pinned, and installs packages from it |
| `config` | installs configuration into Debian's drop-in folders |

## Rules

- No definition replaces a Debian package: names, `provides`, `replaces` and
  `conflicts` are checked against Debian.
- Every download has a `sha256` and a `size`.
- A changed definition raises `revision`; a new `version` resets it to 1.
- `maintainer: Name <email>`; default `FOSSauce <fossauce@tuta.io>`.
- Files go under `/usr` and `/opt/<name>`. `/etc` and `/usr/local` are
  refused, except:
  - `/etc/fonts/conf.d/NN-name.conf`, a link to the same name in
    `/usr/share/fontconfig/conf.avail/`, as Debian's font packages do.
  - `/etc/apt/sources.list.d/debium-<name>.sources` and
    `/etc/apt/preferences.d/debium-<name>.pref`.
  - Kind `apt`: `<repo>-archive-keyring.sources` and `.pref` there, the key
    in `/usr/share/keyrings/`.
- Debian sources come only from Debian's archive for the release. Backports
  keeps Debian's default priority unless pinned.
- `requires` names catalog packages installed first.

## Use

`debium update` downloads the `main` branch. To use a local checkout:

```sh
DEBIUM_CATALOG=~/debium-catalog debium install <name>
```
