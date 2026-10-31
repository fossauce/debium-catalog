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
| `packages/<x>/<name>/package.yaml` | one definition; `<x>` is the first character of `<name>`, or `lib` and the next one (`libf/libfoo`), as in Debian's pool |
| `packages/<x>/<name>/files/` | files a definition installs as they are |
| `repos/<name>.yaml` | a vendor apt repository; kind `apt` names it in `repo` |

## Kinds

| Kind | debium |
|---|---|
| `repack` | packs files upstream already built |
| `source` | compiles upstream source on the machine, as the user, in a sandbox |
| `deb` | installs a vendor `.deb` unchanged, after checks |
| `meta` | depends on Debian packages; may add Debian components, backports and pins |
| `apt` | adds a vendor repository, pinned, and installs packages from it |
| `config` | installs configuration into Debian's drop-in folders |

## Rules

- No definition replaces a Debian package: names, `provides`, `replaces` and
  `conflicts` are checked against Debian. Exceptions: a repository with
  `pin: false`, for a vendor stack that replaces Debian packages on purpose;
  `replaces-debian: true`, while debium's version is newer than Debian's.
- Every download has a `sha256` and a `size`; an apt key has its
  fingerprints.
- A changed definition raises `revision`; a new `version` resets it to 1.
- `maintainer: Name <email>`; default `FOSSauce <fossauce@tuta.io>`.
- Files go under `/usr` and `/opt/<name>`. `/etc` and `/usr/local` are
  refused, except:
  - `/etc/fonts/conf.d/NN-name.conf`, a link to the same name in
    `/usr/share/fontconfig/conf.avail/`, as Debian's font packages do.
  - `/etc/apt/sources.list.d/debium-<name>.sources` and
    `/etc/apt/preferences.d/debium-<name>.pref`.
  - Kind `apt`: `debium-<repo>-keyring.sources` and `.pref` there, the key
    in `/usr/share/keyrings/`; with `cron-opt-out: <name>`,
    `/etc/default/<name>`, which stops the vendor's daily cron job from adding
    a source and a key.
- A repository with `adds-source: <package>`: that vendor package adds the
  source and key; debium adds the pin, unless `pin: false`.
- Debian sources come only from Debian's archive for the release. Backports
  keeps Debian's default priority unless pinned.
- `requires` names catalog packages installed first.
- In `repos/`: `uris`, `suites` and `key.url` take `{codename}` (`trixie`)
  and `{release}` (`13`). Quote them in lists: `["{codename}"]`.
- `user-groups` (kinds `apt`, `deb`): the user joins these groups, created by
  the packages, and leaves them on removal.

## Use

`debium update` downloads the `main` branch. To use a local checkout:

```sh
DEBIUM_CATALOG=~/debium-catalog debium install <name>
```
