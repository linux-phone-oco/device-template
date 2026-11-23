# Device template

**A new phone's lab is a new `device.toml`.** Use this template to make `VENDOR-CODENAME-lab` for a phone
running postmarketOS; everything else - reaching the phone, flashing, provisioning, a test kernel tried
once and falling back by itself, the phone checking its own health after every boot, gathering its
reports into the picture, and a door for agents - comes from
[lpoco lab](https://github.com/linux-phone-oco/lab-tools), which reads this manifest. Surya
([linux-phone-oco/surya-lab](https://github.com/linux-phone-oco/surya-lab)) is the first phone made
this way, not the mould.

## Start

1. **Use this template**, name it `VENDOR-CODENAME-lab`; give it the topics `oco-project` and
   `oco-kind-device-lab` so the observatory finds it.
2. Fill `device.toml`: every value marked `CHANGE`, and `[health]` - what the phone has, what it lacks,
   what has never worked yet. Unknown fields are refused, so a typo cannot pass silently.
3. Put the phone user's password in `~/.config/devlab/CODENAME/password` (mode 600) on the lab machine.

## The base

A device made from this template has its own open base (tad, `data/`): its queue, tries, measurements, decisions,
research and parts. `make setup` installs tad and `ikb`; `ikb --help` lists what can be recorded, `ikb help RECORD`
how. The standing tables follow [grenudi/ideal-knowledge-base](https://github.com/grenudi/ideal-knowledge-base)
(`idealkb.toml`); the device's own are in `tables.toml`. Fill the device (`ikb device ...`) and declare what is
claimed of each part (`ikb part`: status, evidence, date) - [linux-phone-oco/docs](https://github.com/linux-phone-oco/docs)
pulls `parts` (contract in `devicebase.toml`) and derives the device's support status from them; it types nothing of
its own. The phone's queue is `tad q phone-queue`, its keeper's next item `tad q phone-next`; `lpoco lab gather` files runs
with the `test-run` record. Keep the template's own `data/` empty: a new device starts with no rows.

## The conveyor

    lpoco lab flash now IMAGE BOOTLOADER      an image onto the phone, the manifest's fixes baked in
    lpoco lab provision now                   packages and Wi-Fi - and then the phone's health
    lpoco lab suite put now --runner FILE     the health suite onto the phone: it checks itself after every boot
    lpoco lab kernel install NAME ... / try NAME   a test kernel beside stock, booted once - then its health
    lpoco lab phone reboot                    and wait - then the new boot's health
    lpoco lab gather now                      the phone's reports into tests/results and the picture's tables
    lpoco lab mcp                             the phone for any agent (MCP, stdio)

Every change lpoco lab makes ends with the phone's own health report: nobody has to remember to check.
A part the phone never had working is a `gap` - measured on every boot, but not ill health.

## Followed and made once

Every file here is one of three kinds. **Followed**: laid by `bin/follow.sh` of ideal-knowledge-base, at the release a pin
file names - the standing statements and tables from [grenudi/ideal-knowledge-base](https://github.com/grenudi/ideal-knowledge-base)
(`idealkb.toml`), the device layer (the phone's statements and the `devices`, `parts`, `test_runs` and `test_results` tables) from
[linux-phone-oco/docs](https://github.com/linux-phone-oco/docs) (`devicebase.toml`, folder `device-layer/` there). This template
follows them like any device base; change a followed file at its source, not here. **Made once**: the seed listed in
`made-once`, laid when a base is made from this template and then the base's own. **Data**: `data/` and `docs/`.

## Map

A lab made from this template has these top-level folders; each has a README saying what belongs in it.
Add a folder only with its README and a line here.

| folder or file | what it is |
|---|---|
| `device.toml` | the phone, as facts |
| `procedures/` | what a keeper runs on the phone, one whole try per file, as one command |
| `kernel/` | `topics.toml`, the written list the phone's test kernel `int/CODENAME` is assembled from, and config fragments - never patches (they live on `topic/ITEM` branches of linux-phone-oco/linux) |
| `tools/` | the lab's own programs, if any |
| `provision/` | what a freshly flashed phone gets (`packages.txt`) |
| `tests/` | checks by where they run: `ci/`, `host/` (the lab machine), `health/` (the phone); `results/`, the reports the phone wrote (`lpoco lab gather now`) |
| `research/` | the material behind research rows, `research/ABOUT/DATE/` |
| `data/`, `queries/`, `records/`, `tables.toml`, `idealkb.toml`, `devicebase.toml` | the base: its rows, named queries and records, schema and pins |
| `docs/` | pages rendered from data/, for people |
| `bin/` | the base's small install and check scripts |

Never at the top: run logs, worktrees, copies of other repositories. The whole project's map:
[linux-phone-oco/docs MAP.md](https://github.com/linux-phone-oco/docs/blob/main/MAP.md).

## Licence

Data and docs: [CC-BY-SA-4.0](LICENSE). Scripts, statements and configuration: MIT OR Apache-2.0, at your option
([`LICENSE-MIT`](LICENSE-MIT), [`LICENSE-APACHE`](LICENSE-APACHE)). A device tree or dts patch you add: GPL-2.0-only OR BSD-2-Clause,
with an SPDX header. [`REUSE.toml`](REUSE.toml) says it per path, and `bin/licence-check` (`make verify`, CI) keeps new files
covered. CHANGE in a lab made from this template: review these licences, the licence lines in `REUSE.toml` and the copyright holder in the texts.
