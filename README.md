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
5. Use `python-version=all` for the normal run.

The workflow supports Python 3.8 through 3.14. A normal run uses a matrix across
all of them. Python 3.8 through 3.12 check out the matching
`Nuitka-CPython-tests` branch, such as `CPython310` for Python 3.10 or
`CPython312` for Python 3.12. Python 3.13 and 3.14 reuse the latest Nuitka
test harness and install the official `Lib/test` sources from the exact CPython
tag selected by `actions/setup-python`. Older branches exist in the tests repo,
but they need legacy Python provisioning that this workflow does not set up.

For a focused debug run, choose one `python-version`, use `mode=only`, and set a
small `pattern` such as `test_list.py`.

## Local Trial With act

The workflow has been tested locally with `act` using:

```bash
act workflow_dispatch \
  -W .github/workflows/cpython-tests.yml \
  -j cpython \
  --input python-version=3.10 \
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
