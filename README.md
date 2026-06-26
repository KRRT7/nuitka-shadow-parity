# nuitka-shadow-parity

`nuitka-shadow-parity` is a small coordination repository for running Nuitka's CPython
test comparison suite against the `KRRT7/Nuitka` shadow development branch.

The repository intentionally keeps the moving parts as submodules:

- `Nuitka/` tracks `https://github.com/KRRT7/Nuitka` on `shadow-develop`.
- `Nuitka-CPython-tests/` tracks `https://github.com/Nuitka/Nuitka-CPython-tests`.

## Setup

Clone with submodules:

```bash
git clone --recurse-submodules <repo-url>
cd nuitka-shadow-parity
```

For an existing checkout:

```bash
git submodule update --init --recursive
```

## Local Files

Use `.confidential/` for local machine-specific notes, credentials, scratch
configuration, and other files that should not be committed. The directory is
ignored by Git.

## GitHub Actions

The workflow in `.github/workflows/cpython-tests.yml` is manual-only. Once the
repo is published on GitHub:

1. Open the repository on GitHub.
2. Go to **Actions**.
3. Select **CPython tests with Nuitka**.
4. Click **Run workflow**.
5. Start with `mode=only` and `pattern=test_list.py`.

## Local Trial With act

The workflow has been tested locally with `act` using:

```bash
act workflow_dispatch \
  -W .github/workflows/cpython-tests.yml \
  -j cpython310 \
  --input mode=only \
  --input pattern=test_list.py \
  --container-architecture linux/amd64 \
  -P ubuntu-24.04=catthehacker/ubuntu:act-24.04
```

The default trial pattern is intentionally small. Broader patterns can take
substantially longer because each test is compiled and compared with CPython.

If `act` fails with `lookup <nil>: no such host`, add explicit loopback server
addresses:

```bash
--cache-server-addr 127.0.0.1 \
--artifact-server-addr 127.0.0.1
```
