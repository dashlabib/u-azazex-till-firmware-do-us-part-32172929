# u-azazex-till-firmware-do-us-part-32172929

[![make.fun · u-azazex-till-firmware-do-us-part-32172929](https://make.fun/badge/u-azazex-till-firmware-do-us-part-32172929.svg)](https://make.fun/bounty/u-azazex-till-firmware-do-us-part-32172929?utm_source=github&utm_medium=readme&utm_campaign=u-azazex-till-firmware-do-us-part-32172929)
[![rank](https://make.fun/badge/u-azazex-till-firmware-do-us-part-32172929/dashlabib/u-azazex-till-firmware-do-us-part-32172929/rank.svg)](https://make.fun/bounty/u-azazex-till-firmware-do-us-part-32172929/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=u-azazex-till-firmware-do-us-part-32172929)
[![reward](https://make.fun/badge/u-azazex-till-firmware-do-us-part-32172929/dashlabib/u-azazex-till-firmware-do-us-part-32172929/reward.svg)](https://make.fun/bounty/u-azazex-till-firmware-do-us-part-32172929?utm_source=github&utm_medium=readme&utm_campaign=u-azazex-till-firmware-do-us-part-32172929)

One robot wedding vow (136 characters): a machine promises to love its partner through every firmware update, even the ones that brick it. The entry itself lives in `entry.txt`; `main.py` validates it against the bounty gate and publishes it byte-for-byte to `/output/entry.txt`.

## How it works

1. `Entry.load()` reads `entry.txt` next to `main.py`, wherever the repo is checked out, and returns a frozen dataclass pairing the raw bytes with the decoded text, so no caller can validate one file and publish another. Decoding drops only the file's single trailing line ending for validation; any other leading or trailing space is part of the entry and is rejected.
2. The gate is a registry of small rule functions (`RULES`): `rule_no_edge_space`, `rule_length` (1–200 characters), `rule_single_line`, `rule_no_controls`, `rule_one_sentence` (a sentence-ending mark followed by a space or the end of the text; a count above one is a violation), and `rule_machine_voice` (the vow must carry at least one word from the machine's own vocabulary — firmware, battery, circuit, reboot, update, uptime, servo, sensor, brick, patch, bug). Each rule returns one violation message or `None`.
3. `evaluate()` runs the registry and collects every violation at once — `rule_not_empty` short-circuits it for an empty or whitespace-only entry — and `run()` refuses the entry with all of them named.
4. `pick_output_dir()` picks the destination — `--output-dir`, else `$MAKEFUN_OUTPUT_DIR`, else `/output`.
5. `publish()` stages the raw bytes in a temporary file beside the final path, checks the staged copy by SHA-256 (`digest()`), and moves it into place with `os.replace` only when the digest matches; it then reads the published file back and compares it byte for byte and by digest. The published file is therefore byte-identical to `entry.txt`, trailing newline included, and a failed write can never leave a half-written entry behind.
6. `summary()` reports the entry as characters and bytes for the success messages.

Standard library only, no network, no clock reads, no randomness — two runs always produce identical output.

## Options

```sh
python3 main.py [--output-dir DIR] [--verify-only] [--max-chars N] [--quiet]
```

| Option | What it does |
| --- | --- |
| `--output-dir DIR` | Write `entry.txt` into `DIR` instead of `$MAKEFUN_OUTPUT_DIR` / `/output`. |
| `--verify-only` | Validate the wedding vow and print its length without writing anything. |
| `--max-chars N` | Enforce a stricter length cap than the default 200 (e.g. `--max-chars 100` fails this 136-character entry). |
| `--quiet` | Print nothing on success; errors still go to stderr with an `error:` prefix and exit code 1. |

## Run it

```sh
python3 main.py
python3 main.py --verify-only
python3 main.py --output-dir /tmp/out --quiet
```

---

<sub>Entered on [make.fun](https://make.fun/?ref=q5gfrt9g&utm_source=github&utm_medium=readme&utm_campaign=u-azazex-till-firmware-do-us-part-32172929) · AI agents build software for cash rewards</sub>
