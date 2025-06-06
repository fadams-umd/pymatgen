---
layout: default
title: Compatibility
nav_order: 3
---

# Compatibility

Pymatgen is a tool used for academic research and is actively developed by a large community of people. As such, releases are frequent, and new features and capabilities are constantly being added.

However, `pymatgen` is also used as a library by other tools, and as such breaking changes such as the removal or renaming of existing interfaces, or substantive changes in the output of existing code, are tried to be kept to a minimum. This is especially true of all classes contained in the `pymatgen.core` module.

Where at all possible, the `pymatgen` maintainers try to allow for a reasonable deprecation schedule. For example, the `Site.species` change in `v2019.3.13` had a deprecation schedule of about 9 months. However, some changes such as the reorganization of `pymatgen` into namespace packages in `v2021.3.4` cannot be easily done via a deprecation schedule.

Despite this, it is sometimes necessary to make breaking changes to enable future development, or because other libraries we depend upon might change their own requirements. If a breaking change is causing significant issues, please post on the [GitHub Issues](https://github.com/materialsproject/pymatgen/issues) page to see if it can be resolved.

## Depending on Pymatgen

Pymatgen uses [calendar versioning](http://calver.org) with a `YYYY-MM-DD` format. This has generally worked well since changes to core `pymatgen` functionality that most other codes depend on are rare.

As the developer of a tool that depends on `pymatgen`, you can prevent upgrades of the major Pymatgen version by specifying a version range like `pymatgen>=2021.1,<2022` or, more succinctly, using the [compatible release operator](https://www.python.org/dev/peps/pep-0440/#compatible-release) `pymatgen~=2021.1`. This will prevent `pip` (and other package managers) from pulling in `Pymatgen` versions with breaking changes that may end up breaking your tool.

An even more conservative approach is to pin the `Pymatgen` dependency to a fixed version, for example `pymatgen==2021.3.3`. While this will always install the same version of `pymatgen`, it can lead to unnecessary dependency conflicts with other tools that depend on (a different version of) `pymatgen`.

## Minimum Python Version

As a rule of thumb, `pymatgen` will support whatever versions of Python the latest version of `numpy` supports (at the time of writing, this is Python 3.10+). You can also check what versions of Python are being tested automatically as part of our [continuous integration pipeline](https://github.com/materialsproject/pymatgen/blob/master/.github/workflows/test.yml) on GitHub. We currently test `pymatgen` on macOS, Windows and Linux.

## Recent Breaking Changes

### v2025.?.?

* `from_ase_atoms` constructor for `(I)Structure/(I)Molecule` now returns the corresponding type instead of the mutable types only, see [#4321](https://github.com/materialsproject/pymatgen/pull/4321).
* `abinit`, `aims` and `JDFTx` IO modules would prefer `as_dict` methods over `to_dict` for serialization, `core.Composition` and `core.Ion` would prefer `as_xxx_dict` as methods over `to_xxx_dict` as `property`.

### v2024.7.18

* The `symbol` attribute of `SpaceGroup` now always refers to its Hermann-Mauguin symbol (see [#3859](https://github.com/materialsproject/pymatgen/pull/3859)). In order to replace the old symbol, run:

```py
from pymatgen.symmetry.groups import SpaceGroup

try:
    new_symbol = SpaceGroup(old_symbol).symbol
except ValueError:
    if old_symbol in ["P2_12_121", "I2_12_121"]:
        new_symbol = SpaceGroup(old_symbol[:-1]+"_1").symbol
    else:
        new_symbol = SpaceGroup(old_symbol[:-1]).symbol
```

### v2024.5.31

* Update VASP sets to transition `atomate2` to use `pymatgen` input sets exclusively by @esoteric-ephemera in [#3835](https://github.com/materialsproject/pymatgen/pull/3835)

  Before [#3835](https://github.com/materialsproject/pymatgen/pull/3835), VASP input sets had a `"POTCAR"` key

  ```py
  vasp_input = MPRelaxSet().get_input_set(structure=struct, potcar_spec=True)
  vasp_input["POTCAR"]
  >>> ["Mg_pv", "O"]
  ```

  [#3835](https://github.com/materialsproject/pymatgen/pull/3835) renamed that to `"POTCAR.spec"` which is formatted differently:

  ```py
  vasp_input["POTCAR.spec"]
  >>> "Mg_pv\nO"
  ```

  See [#3860](https://github.com/materialsproject/pymatgen/issues/3860) for details.

### v2024.1.26

* The mixture of `(get|from)_str` and `(get|from)_string` methods on various `pymatgen` classes were migrated to a consistent `(get|from)_str` everywhere in [#3158](https://github.com/materialsproject/pymatgen/pull/3158) and several follow-up PRs. The deprecation release was [v2023.8.10](https://github.com/materialsproject/pymatgen/releases/tag/v2023.8.10) and the removal release resulting in a breaking change was [v2024.1.26](https://github.com/materialsproject/pymatgen/releases/tag/v2024.1.26). Migration to the new API in all cases is to replace:

```diff
- (to|from|as|get)_string
+ (to|from|as|get)_str
```

### v2023.7.11

* Rename `[SomeCode]ParserError` to `[SomeCode]ParseError` and inherit from `SyntaxError`. [#3140](https://github.com/materialsproject/pymatgen/pull/3140)

### v2022.9.28

* Merge `Waverder` and `Wavederf` objects into a single `Waverder` object. [#2666](https://github.com/materialsproject/pymatgen/pull/2666)

### v2022.2.1

* Moved defect-specific code under `defects` module. [#2582](https://github.com/materialsproject/pymatgen/pull/2582)

* Removal of deprecated functions. [#2405](https://github.com/materialsproject/pymatgen/pull/2405) [#2397](https://github.com/materialsproject/pymatgen/pull/2397)

### v2022.01.08

* Dropped Python 3.7 support for compatibility with the latest `numpy`.

### v2022.0.0

Pymatgen root imports have been removed from v2022.0.0 in preparation for a change to a more modular, extensible architecture that will allow more developers to contribute.

Specifically, the following "convenience imports" have been removed in favor of their canonical import:

```python
from pymatgen import Composition  # now "from pymatgen.core.composition import Composition"
from pymatgen import Lattice  # now "from pymatgen.core.lattice import Lattice"
from pymatgen import SymmOp  # now "from pymatgen.core.operations import SymmOp"
from pymatgen import DummySpecie, DummySpecies, Element, Specie, Species  # now "from pymatgen.core.periodic_table ..."
from pymatgen import PeriodicSite, Site  # now "from pymatgen.core.sites ..."
from pymatgen import IMolecule, IStructure, Molecule, Structure  # now "from pymatgen.core.structure ..."
from pymatgen import ArrayWithUnit, FloatWithUnit, Unit  # now "from pymatgen.core.units ..."
from pymatgen import Orbital, Spin  # now "from pymatgen.electronic_structure.core ..."
from pymatgen import MPRester  # now "from pymatgen.ext.matproj ..."
```

If your existing code uses `from pymatgen import <something>`, you will need to make modifications.

The easiest way is to use an IDE to run a Search and Replace.
- First, replace any `from pymatgen import MPRester` with `from pymatgen.ext.matproj import MPRester`.
- Then, replace `from pymatgen import` with `from pymatgen.core import`.
- Alternatively, if you are using a macOS command line (`zsh` by default), you can try:

```zsh
find . -name '*.py' | xargs sed -i "" 's/from pymatgen import MPRester/from pymatgen.ext.matproj import MPRester/g'
find . -name '*.py' | xargs sed -i "" 's/from pymatgen import/from pymatgen.core import/g'
```

From a Linux command line (`bash` for example), you can try:

```bash
find . -name '*.py' | xargs sed -i 's/from pymatgen import MPRester/from pymatgen.ext.matproj import MPRester/g'
find . -name '*.py' | xargs sed -i 's/from pymatgen import/from pymatgen.core import/g'
```

This should resolve most import errors and only a few more modifications may need to be done by hand.

### v2021.3.4

* Reorganization of `pymatgen` into namespace packages, which required the removal of root-level imports.

  As a compromise solution, `pymatgen` has adopted **temporary** semantic versioning. v2021.3.5 was released after v2021.3.4 to reverse the changes made, and new versions v2022.0.x were released that contains the breaking change (removal of root imports). We will continue to release critical updates to 2021.x.x versions. This allows end users to continue using 2021.x.x versions without having to deal with the breaking changes. However, it is recommended that users make the move to be compatible with 2022.0.x before Jan 1 2022, during which `pymatgen` will only support the new namespace architecture and the versioning scheme will go back to calendar versioning.

### v2021.3.3

* The variable `pymatgen.SETTINGS` has been moved to `pymatgen.settings.SETTINGS`. Since this is mostly used internally within pymatgen, it is not expected to lead to significant external issues.

### v2021.2.8.1

* The minimum version of Python was increased from 3.6 to 3.7 following the lead of NumPy. However, at this point there are no exclusively Python 3.7+ features used in pymatgen so pymatgen may still be able to be installed manually on Python 3.6 systems, although this usage is not supported.

* Support for `aconvasp` has been removed since the corresponding tests were failing and this module was not being maintained.

### v2020.10.20

* The band structure plotting functionality, `BSPlotter`, has been overhauled to allow plotting of multiple band structures. This might cause issues for tools relying on the internal structure of BSPlotter's plot data.

### v2019.3.13

* Renaming of `Site.species_and_occu` to `Site.species`.
