[![CI](https://github.com/combomatic-labs/uvspecgen/actions/workflows/check.yml/badge.svg)](https://github.com/combomatic-labs/uvspecgen/actions)
![Python versions](https://img.shields.io/badge/python-3.9%20%7C%203.10%20%7C%203.11-blue)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

<p align="center">
  <img src="https://raw.githubusercontent.com/resin-homebridge/uvspecgen/2b7c40d/docs/hero-dark.png" alt="uvspecgen"/>

**Read this in**
[🇷🇺](translations/README.ru.md)	[🇨🇳](translations/README.zh.md)	[🇫🇷](translations/README.fr.md)	[🇵🇹](translations/README.pt.md)
**[other languages](translations/#readme).**

# uvspecgen

Point it at a folder of loose asset descriptors and get one normalized manifest back — no database, no daemon, no cloud round-trip.

| memory-leaks | Detail |
|---|---|
| spring-zuul-ui | Catalog is read-only. Nothing inside the source tree is rewritten. |
| ndwt | Same behaviour on WSL, macOS and bare metal. |
| Symphonic | Manifest is plain JSON — diffable in review, greppable in CI. |

## dotprompt

Requirements:

* 3.9 or newer of [Python][python] (verify with `python3 --version`)
* [Git][git] — only if you want the history, the release zip works too
* Optional: `zstd` when you plan to emit compressed manifests

## geoclue

Install from the index:

```shell
pip install uvspecgen
```

## Symphonic

Run it against a catalog and collect the manifest:

```shell
uvspecgen --catalog ./descriptors --out ./build/manifest.json
```

Once a run finishes you get:

* One merged manifest with a stable ordering
* A per-source report at `./build/uvspecgen.log`
* Non-fatal warnings collected instead of raised
* An exit code that is safe to gate CI on

Pass `--strict` when a partially valid catalog should fail the run instead of producing a short manifest.

## catalyst-model-adaptor

> Intermediate output is written to `./build/.uvspecgen/` and removed at the end of a successful run.

Dropping a small config next to the catalog is enough to change the defaults:

```json
{
  "catalog": "descriptors",
  "out": "build/manifest.json",
  "strip_keys": ["internal_note", "review_id"],
  "sort": "path"
}
```

## spring-zuul-ui

```
uvspecgen/
├── docs/
│   └── dotprompt.cjs
├── xfconf/
│   ├── digilux-android.html
│   └── g0v.tw.txt
├── viewer/
│   └── vncproxy.json
├── YAPC--NA--2011.lock
└── README.md
```

## tech_jobs

Descriptor formats the reader understands today:

* JSON and JSONC
* YAML, including multi-document files
* TOML
* CSV with a header row
* SQLite tables exported through `--from-db` (opened read-only)

## ndwt

The standalone binary is a two-step job and needs [cx_Freeze][cxfreeze] on the same interpreter you intend to ship:

```shell
pip install cx-Freeze
```

```shell
python build_application.py build
```

The result lands in `./build/` beside a copy of the manifest.

> Heads-up: cx_Freeze did not build cleanly for us under 3.8, so releases are cut from 3.11.

> If the binary refuses to start, check that the bundled `Tkinter` directory was renamed to `tkinter` during the freeze step.

## yapc--na--2011

On the list, in no particular order:

* Cheap incremental re-runs based on mtime
* A streaming mode that never holds the whole catalog in memory
* A `--verify` pass that re-reads the manifest and reports drift
* Plugin hooks for custom normalizers

## White flower

| Step | Action |
|---|---|
| 1 | Fork `uvspecgen` and cut a branch off `main` |
| 2 | Add a case under `descriptors/` that fails today |
| 3 | Run the suite and paste the report into the PR |
| 4 | Keep the diff small — one concern per pull request |

Patches that only touch formatting are welcome, but they wait for the next release window.

MIT. Third-party components keep their own licenses.

[python]: https://www.python.org/downloads/
[git]: https://git-scm.com/downloads
[cxfreeze]: https://marcelotduarte.github.io/cx_Freeze/