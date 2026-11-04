# Development setup

## Source baseline

- Application version declared in `app_Main.py`: **8.994**
- Base application: FlatCAM BETA
- Python launcher minimum declared in `FlatCAM.py`: Python 3.5
- Maintainer's practical development baseline: Python 3.8.x

Use a dedicated virtual environment. Avoid installing the legacy dependency set into a system Python installation.

## Windows

```bat
py -3.8 -m venv .venv
.venv\Scripts\activate
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
python FlatCAM.py
```

If GDAL or another native package cannot be installed from PyPI, use a wheel that matches the exact Python version and architecture, or the appropriate system package. Do not mix 32-bit and 64-bit wheels.

## Linux

The repository includes `setup_ubuntu.sh` and a `Makefile` from the upstream source tree. Review those scripts before running them, then install system dependencies and launch with:

```sh
python3 FlatCAM.py
```

The Linux setup has not been revalidated as part of the current edition preparation.

## Code organization

- `app_Main.py`: main application class, startup and application-level workflows
- `appGUI/`: Qt widgets and object property panels
- `appObjects/`: object models and geometry operations
- `appTools/`: user-facing manufacturing tools
- `appParsers/`: Gerber, Excellon, SVG and other import parsers
- `preprocessors/`: machine-specific CNC output formatting
- `tclCommands/`: Tcl command implementations

## Local checks

Run a syntax compilation pass from the repository root:

```bat
python -m compileall -q .
```

This checks Python syntax only. It does not import all dependencies, start the Qt GUI, validate geometry, or prove generated G-code is safe.

## Change workflow

1. Create a branch for one focused change.
2. Inspect the existing implementation before editing.
3. Keep changes small and avoid unrelated formatting churn.
4. Add or update documentation for user-visible behavior.
5. Run syntax checks and focused manual tests.
6. Record the exact application version, OS and Python version used.
7. Open a pull request with before/after screenshots for UI changes where useful.

Never commit generated PCB production files, private designs, machine credentials, local virtual environments, caches or personal configuration.
