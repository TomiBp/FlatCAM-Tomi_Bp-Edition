# FlatCAM — Tomi Bp Edition

**A personal, community-maintained edition of FlatCAM BETA 8.994** for preparing PCB CNC jobs.

This repository contains the FlatCAM 8.994 source tree with additional workflow features and UI changes maintained by Tomi Bp. It is based on the upstream FlatCAM project and retains its original architecture and dependencies.

> **Status:** Unreleased development edition. The source has received static checks, but a complete GUI/CNC workflow test has not yet been performed on every supported platform. Always simulate and inspect generated toolpaths before machining.

## Highlights in this edition

- **EAGLE 10× Excellon coordinate correction** — an Excellon-object checkbox applies a 0.1 coordinate scale to drill/slot positions while leaving tool diameters unchanged. It is enabled by default in the current source.
- **NCC board perimeter** — optional perimeter geometry is appended to the largest NCC tool that actually cleared copper. The current implementation derives the perimeter from the convex hull of the selected NCC reference geometry and offsets it by half the selected tool diameter.
- **NCC tool-to-tool overlap profiles** — overlap values can be saved per tool diameter in application defaults and reused when working with matching tools.
- **NCC interface refinements** — layout and control improvements, including the board-perimeter option and explicit overlap-save control.
- **EAGLE icon** — monochrome EAGLE mark used beside the Excellon coordinate-correction control.

See [Features](docs/FEATURES.md) for behavior and limitations, and [Testing](docs/TESTING.md) for the current verification status.

## Base project and attribution

This edition is based on **FlatCAM BETA 8.994**, originally developed by Marius Stanciu, building on the FlatCAM project by Juan Pablo Caram.

This repository is a personal/community modification. It is not the official FlatCAM project and is not endorsed by the upstream maintainers.

- **FlatCAM BETA source repository (Marius Stanciu):** https://bitbucket.org/marius_stanciu/flatcam_beta
- **Original FlatCAM source repository (Juan Pablo Caram / jpcgt):** https://bitbucket.org/jpcgt/flatcam
- **Official FlatCAM website:** https://www.flatcam.org/

The upstream license and copyright notices are retained in [`LICENSE`](LICENSE). Contributions and bundled third-party components remain subject to their respective licenses and notices.

## Requirements

The application is a legacy desktop Python application. The source launcher declares Python **3.5+**; for this edition, the known working development environment is:

- Windows 7 / Windows 10, 64-bit (Windows 7 compatibility is environment-dependent)
- Python 3.8.x, 64-bit
- PyQt5
- The Python packages listed in [`requirements.txt`](requirements.txt)
- Additional native dependencies such as GDAL may require platform-specific wheels or system packages.

The full dependency stack is not guaranteed to install on current Python releases. Prefer a clean Python 3.8 environment when reproducing the legacy setup.

## Run from source (Windows)

1. Install 64-bit Python 3.8 and make sure `python` and `pip` resolve to that interpreter.
2. Open Command Prompt in the repository root.
3. Create and activate a virtual environment:

   ```bat
   py -3.8 -m venv .venv
   .venv\Scripts\activate
   python -m pip install --upgrade pip
   ```

4. Install dependencies:

   ```bat
   python -m pip install -r requirements.txt
   ```

   Some packages (especially PyQt5/GDAL and geospatial dependencies) may need compatible prebuilt wheels. See [Development setup](docs/DEVELOPMENT.md).

5. Launch:

   ```bat
   python FlatCAM.py
   ```

For Linux, see the existing [`setup_ubuntu.sh`](setup_ubuntu.sh) and [`Makefile`](Makefile). macOS setup has not been revalidated for this edition.

## Repository guide

- `appGUI/` — Qt user interface
- `appObjects/` — Gerber, Excellon, Geometry and CNC job objects
- `appTools/` — manufacturing tools, including NCC
- `appParsers/` — input file parsers
- `appEditors/` — Gerber, Excellon, Geometry and G-code editors
- `preprocessors/` — CNC machine/preprocessor output formats
- `tclCommands/` — Tcl command interface
- `assets/` — application resources and icons
- `locale/` — translations

## Documentation

- [Feature behavior and caveats](docs/FEATURES.md)
- [Development and contribution workflow](docs/DEVELOPMENT.md)
- [Verification and test status](docs/TESTING.md)
- [Changelog](CHANGELOG.md)

## Safety note

FlatCAM generates manufacturing geometry and CNC code. A visually plausible preview is not proof of a safe toolpath. Check units, origin, tool diameters, depths, feeds, spindle settings, travel heights, machine limits and workholding. Run a simulator or air-cut where practical. The maintainer and contributors cannot guarantee the safety of machine-specific output.

## Contributing

Please read [CONTRIBUTING.md](CONTRIBUTING.md) before submitting a pull request. Bug reports should include the operating system, Python version, FlatCAM version, input type, exact steps, expected result, actual result and relevant logs. Do not attach private PCB designs or machine credentials.

## License

See [`LICENSE`](LICENSE). The upstream MIT license notice is retained.
