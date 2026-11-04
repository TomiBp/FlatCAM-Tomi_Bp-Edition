# FlatCAM — Tomi Bp Edition

**A FlatCAM BETA 8.994 közösségi továbbfejlesztett változata PCB-tervezéshez és CNC-marási munkák előkészítéséhez.**

Ez a repository a FlatCAM 8.994 forráskódját tartalmazza Tomi Bp által készített munkafolyamat- és kezelőfelületi fejlesztésekkel. Az eredeti FlatCAM-projektből indul ki, annak architektúráját és függőségeit megtartva.

**A legfontosabb fejlesztés az NCC Tool-to-tool overlap kezelése**, amely a többszerszámos rézeltávolításnál átfedést biztosít az egymást követő szerszámok megmunkálási területei között. Ez segít megszüntetni a szerszámhatárokon visszamaradó vékony rézcsíkokat és sorját, javítja a felület folytonosságát, és csökkentheti az utólagos tisztításból vagy ismételt marásból eredő felesleges gépidőt.

> **Állapot:** fejlesztői, még nem kiadott változat. A forráskód statikus ellenőrzéseken átesett, de a teljes grafikus felület és a CNC-munkafolyamat minden támogatott platformon még nincs végigtesztelve. Marás előtt mindig szimuláld és ellenőrizd a generált szerszámpályákat.

---

## 🇭🇺 Magyar bemutató

### A projektről

A Tomi Bp Edition a FlatCAM BETA 8.994 közösségi módosításokkal továbbfejlesztett változata. Célja a NYÁK (PCB) gyártásához kapcsolódó CNC-munkafolyamatok hatékonyabbá és megbízhatóbbá tétele. **A projekt központi fejlesztése az NCC (Non-Copper Clearing – rézmentes területek kimarása) többszerszámos Tool-to-tool overlap funkciója.** Emellett kezelőfelületi fejlesztéseket és EAGLE Excellon koordinátakorrekciót is tartalmaz.

Ez egy független, nem hivatalos közösségi kiadás. Nem áll kapcsolatban az eredeti FlatCAM fejlesztőivel, és azok nem vállalnak felelősséget az itt végzett módosításokért.

### Saját fejlesztések

#### 1. NCC Tool-to-tool overlap – a kiadás fő fejlesztése

A többszerszámos NCC megmunkálás egyik kritikus pontja a szerszámváltások közötti átmenet. Ha a kisebb szerszám csak pontosan addig dolgozik, ameddig az előző, nagyobb szerszám által kimart terület véget ér, a határon vékony, megmaradt rézcsík vagy sorja maradhat. A Tomi Bp Edition ezt a problémát kezeli a **Tool-to-tool overlap** funkcióval.

- A kisebb, következő szerszám a beállított mértékben **ráfed az előző, nagyobb szerszám által már megtisztított területre**.
- Az átfedés a szerszám szélességének százalékában állítható.
- Ezzel csökkenthető a szerszámhatárokon visszamaradó réz, a sorjaképződés és az utólagos kézi tisztítás szükségessége.
- A folyamatosabb átmenet megbízhatóbb, egyenletesebb rézeltávolítást tesz lehetővé.
- A jól beállított többszerszámos munkamenet csökkentheti a hibásan tisztított részek miatti ismételt marást és a felesleges gépidőt.
- Minden szerszámátmérőhöz külön **Tool-to-tool overlap** érték menthető.
- Az alapértelmezett kezdeti átfedési érték 60%.
- A mentés külön **Save tool-to-tool overlap** gombbal történik; a profilok átmérő szerint, egymástól függetlenül tárolódnak.

Ez a projekt elsődleges funkcionális fejlesztése: nem egyszerű kezelőfelületi kényelmi módosítás, hanem a többszerszámos NCC megmunkálás minőségét és hatékonyságát célzó változtatás. Az optimális átfedési érték a szerszámoktól, az anyagtól és a marási beállításoktól függ, ezért azt próbamunkával kell ellenőrizni.

#### 2. Generate Geometry with All Tools

Az NCC eszköz új **Generate Geometry with All Tools** műveletet kapott.

- Az eredeti **Generate Geometry** funkció továbbra is a kijelölt szerszámokkal dolgozik.
- Az új művelet az összes felvett szerszámmal indítja a geometria generálását, a kijelöléstől függetlenül.
- A megoldás a meglévő NCC generáló rutint használja, nem egy külön geometriai algoritmust.

A funkció további gyakorlati tesztelést igényel, különösen területkijelölés és több szerszámos munkák esetén.

#### 3. NCC Board perimeter – NYÁK külső kontúrja

- Az NCC felületen külön **Board perimeter** opció kapcsolható.
- Bekapcsolásakor a program a rézeltávolítást ténylegesen végző legnagyobb NCC szerszámhoz hozzáadja a külső kontúr marópályáját.
- A jelenlegi megvalósítás a kiválasztott NCC referencia-geometria konvex burkából (convex hull) indul ki, majd a kontúrt a szerszám sugarával kifelé tolja el.
- Emiatt ez nem minden esetben követi a bonyolult, homorú NYÁK-körvonalat. A generált kontúrt marás előtt mindig ellenőrizni kell.

#### 4. EAGLE 10× Excellon koordinátakorrekció

- Az Excellon objektumon külön **EAGLE 10x coordinate correction** opció található.
- A korrekció a fúró- és horonykoordinátákra 0,1-es skálázást alkalmaz.
- A szerszámátmérőket nem módosítja.
- A jelölőnégyzet az aktuális forrásban alapértelmezetten be van kapcsolva.
- A funkció mellett monokróm EAGLE ikon jelenik meg.

#### 5. NCC kezelőfelületi finomítások

Az NCC felület elrendezése és vezérlőelemei több ponton módosultak, többek között a külön overlap-mentés és a Board perimeter opció elhelyezése érdekében.

### Telepítés röviden

A repository Python-forráskódot tartalmaz, nem önálló telepítőt. A függőségek és a szükséges rendszerkönyvtárak telepítése után a program a forráskönyvtárból indítható.

Windows alatt (Python 3.8, 64 bit):

```bat
py -3.8 -m venv .venv
.venv\Scripts\activate
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
python FlatCAM.py
```

Egyes csomagok – különösen a PyQt5, GDAL és a geoinformatikai függőségek – platformfüggő, előre fordított csomagokat vagy külön rendszerkönyvtárakat igényelhetnek. Részletesebb információért lásd a [fejlesztői útmutatót](docs/DEVELOPMENT.md).

### Tesztelési állapot és biztonság

A forrás Python-fájljainak szintaktikai ellenőrzése sikeres volt, és a dokumentált statikus funkcióellenőrzések is lefutottak. Ez azonban nem helyettesíti a teljes grafikus felületi és valós CNC-munkafolyamat tesztelését.

A generált G-kódot mindig ellenőrizd: egységek, nullpont, szerszámátmérők, mélységek, előtolások, orsóbeállítások, biztonsági magasságok, géphatárok és munkadarab-rögzítés. Lehetőség szerint használj szimulátort vagy levegőben végzett próbamarást.

---

## 🇬🇧 English

**A personal, community-maintained edition of FlatCAM BETA 8.994** for preparing PCB CNC jobs.

This repository contains the FlatCAM 8.994 source tree with additional workflow features and UI changes maintained by Tomi Bp. It is based on the upstream FlatCAM project and retains its original architecture and dependencies.

> **Status:** Unreleased development edition. The source has received static checks, but a complete GUI/CNC workflow test has not yet been performed on every supported platform. Always simulate and inspect generated toolpaths before machining.

### Main improvement: NCC tool-to-tool overlap

The central functional improvement in this edition is the **tool-to-tool overlap for multi-tool NCC (Non-Copper Clearing)**. When moving from a larger clearing tool to a smaller one, the next tool deliberately overlaps the area already cleared by the previous, larger tool. This overlap helps prevent thin copper ridges and burrs from being left along tool-boundary transitions, improves continuity between clearing regions, and can reduce avoidable machine time spent on re-clearing or remedial cleanup.

- The overlap is configurable as a percentage of the current tool width.
- A separate profile can be saved for each tool diameter.
- The initial default overlap is 60%.
- Saved profiles are reused for matching tool diameters.

Correct overlap settings depend on the tool, material and machining parameters and should be validated with a test job.

### Other improvements

- **EAGLE 10× Excellon coordinate correction** — an Excellon-object checkbox applies a 0.1 coordinate scale to drill/slot positions while leaving tool diameters unchanged. It is enabled by default in the current source.
- **NCC board perimeter** — optional perimeter geometry is appended to the largest NCC tool that actually cleared copper. The current implementation derives the perimeter from the convex hull of the selected NCC reference geometry and offsets it by half the selected tool diameter. It does not reproduce every concave board outline.
- **Generate Geometry with All Tools** — an additional NCC action that runs the existing geometry-generation routine with every tool in the tool table, regardless of the current selection. Further real-world validation is still needed, especially with area selection and multi-tool jobs.
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

Please read [`CONTRIBUTING.md`](CONTRIBUTING.md) before submitting a pull request. Bug reports should include the operating system, Python version, FlatCAM version, input type, exact steps, expected result, actual result and relevant logs. Do not attach private PCB designs or machine credentials.

## License

See [`LICENSE`](LICENSE). The upstream MIT license notice is retained.

---

**Tomi Bp Edition — practical improvements for PCB CNC workflows.**
