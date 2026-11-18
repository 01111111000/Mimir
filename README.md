# SimulacraBench — team MIMIR — World Bank submission `wb1112ps`

Code of team MIMIR's entry to [SimulacraBench](https://github.com/SituatedEvals/public), the NeurIPS 2026 competition on predicting survey answers (Codabench test-phase submission 1057565).

## What this repository holds

| File | |
|---|---|
| `public-wb1112ps-7bae656c.zip` | The code of the submitted archive, with comments and docstrings removed |
| Size | 135,081 bytes |
| SHA-256 | `7bae656ce42fb4f589859195c2761930c3807ff88d2a3ecf25e7879d70126e5f` |

The ZIP unpacks to `public-wb1112ps/`: the 37 Python modules that the submission imports, its unchanged `requirements.txt`, `download_weights.py`, and its own `README.md`, `LICENSE` and `NOTICE`.

The archive that was submitted to Codabench also bundled the two model checkpoints, so it is too large for this repository:

| Submitted archive | |
|---|---|
| File | `simulacrabench-wb1112ps-c2e7eee1.zip` |
| Size | 896,974,708 bytes |
| SHA-256 | `c2e7eee14d0aa81bd81e1b65803424a23572aa1466fc201fc1d9d6c0b631fbe9` |

The technical report cites commit `1318157fe803abf7b54dbbb99862e219384b8674`, which holds the ZIP. Later commits add only this README and the licence file; the ZIP is unchanged.

## Run it

Python 3.13 was used. The competition runtime is 8 CPUs, 16 GB of RAM and one H100 GPU, with no network during prediction.

Unpack the code, install its requirements and fetch the two checkpoints:

```bash
unzip public-wb1112ps-7bae656c.zip
cd public-wb1112ps
pip install -r requirements.txt
python download_weights.py
SUBMISSION=$(pwd)
```

`download_weights.py` downloads the checkpoints from Hugging Face at pinned revisions, writes them where the code loads them, and checks the SHA-256 that the code checks at import:

| Checkpoint | Source | Revision |
|---|---|---|
| TabICL v2 classifier | `jingang/TabICL`, `tabicl-classifier-v2-20260212.ckpt` | `4dcd344ece2c00be9e831fdd35bed57b5ad83e19` |
| Kumo Tabular large classifier | `nvidia/Kumo-Tabular`, `large/classifier.pt` | `3c3e10bbdb590ace29e7026847f92db3c603096d` |

Then score it with the official kit, at the revision the submission was developed against:

```bash
git clone https://github.com/SituatedEvals/public ../simulacrabench-kit
cd ../simulacrabench-kit
git checkout aa03186f8f083c7785a1a566c802c2077925e265
pip install -r requirements.txt
python make_sandbox.py --schema data/world_bank.json --out _sandbox/world_bank
python score.py --submission "$SUBMISSION" --data _sandbox/world_bank --schema data/world_bank.json --phase 2
```

Replace `world_bank` with `unicef` or `unhcr` for the other two instruments, and `--phase 2` with `--phase 1` for the development phase. `score.py` runs one instrument per call; the official grader calls the three instruments in one process, on the real data. The sandbox holds synthetic data of the real shape, so its scores are not leaderboard scores.

The `README.md` inside the ZIP gives the same steps in more detail.

## Licence

Apache License 2.0, see `LICENSE`. Third-party components (TabICL, Kumo Tabular, `structured-data-models`) keep their own licences, listed in the `NOTICE` file inside the ZIP.
