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
| `packages/<x>/<name>/files/` | `local` inputs |
| `indexes/<name>.yaml` | a vendor apt repository debium reads; never added to apt, except by a `vendor-stack` |

## Kinds

| Kind | debium |
|---|---|
| `package` | builds a .deb from inputs; rebuilds vendor .debs without their scripts |
| `debian` | adds Debian components, backports, pins or i386 |
| `vendor-stack` | adds a vendor repository to apt, pinned; its packages and scripts as they are |

## Inputs

| Input | Checked by |
|---|---|
| `url` | `sha256` and `size` |
| `apt: {index, package}` | the index, signed by its key's `fingerprints` |
| `local` | the catalog |
| `user: {path, match}` | the user's own files |

`to` installs a single input file without a build script; `build.script`
installs the rest from `$SRC` into `$DESTDIR`.

## Rules

- No definition replaces a Debian package: names, `provides`, `replaces` and
  `conflicts` are checked against Debian. Exceptions: `replaces-debian: true`,
  while debium's version is newer; a `vendor-stack` with `pin: false`.
- Definitions have no scripts; vendor scripts never run, except in a
  `vendor-stack`. Scripts are generated from the package's paths.
- A definition with vendor `.deb` inputs has only those and is named after
  one of them. Its `build.script` adds what the vendor's scripts would have
  (links, icons, menu entries) as files in that package.
- A changed definition raises `revision`; a new `version` resets it to 1.
- `maintainer: Name <email>`; default `FOSSauce <fossauce@tuta.io>`.
- Files go under `/usr` and `/opt/<name>`. `/etc` and `/usr/local` are
  refused, except `/etc/fonts/conf.d/NN-name.conf`, a link to the same name in
  `/usr/share/fontconfig/conf.avail/`.
- In `indexes/`: `uri`, `suite` and `key.url` take `{codename}` (`trixie`)
  and `{release}` (`13`).
- `requires` names definitions installed first.
- `user-groups`: the user joins these groups and leaves them on removal.
- Build scripts run in a sandbox, without network unless `build.network:
  true`.

## Use

`debium update` downloads the `main` branch. To use a local checkout:

```sh
DEBIUM_CATALOG=~/debium-catalog debium install <name>
```
