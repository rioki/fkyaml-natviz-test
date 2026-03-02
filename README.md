# fkYAML vcpkg natvis Reproduction

Minimal reproducible example for a configuration failure when consuming
`fkYAML` via vcpkg manifest mode.

## Problem

When linking against `fkYAML::fkYAML`, CMake fails during configure with:

```
Cannot find source file:
.../vcpkg_installed/<triplet>/fkYAML.natvis
```

The imported target `fkYAML::fkYAML` references a `.natvis` file via
`INTERFACE_SOURCES`, but the referenced file does not exist in the
installed vcpkg package.

This breaks configuration even when using non-Visual Studio generators
such as Ninja.

## Environment

- Windows 10 / 11 x64
- CMake ≥ 3.21
- vcpkg (62159a45e1)
- Generator: Ninja
- Triplet: `x64-windows`

## Repository Contents

```

CMakeLists.txt
vcpkg.json
test.cpp

```

The project simply links against `fkYAML::fkYAML`.

## Steps to Reproduce

## Configure using the vcpkg toolchain (manifest mode is automatic):

```
cmake -S . -B build -G Ninja ^
-DCMAKE_TOOLCHAIN_FILE=C:\path\to\vcpkg\scripts\buildsystems\vcpkg.cmake ^
-DVCPKG_TARGET_TRIPLET=x64-windows
```

3. Observe configuration failure during `find_package(fkYAML CONFIG REQUIRED)`.

## Expected Behavior

- Configuration should succeed.
- Either:
  - The referenced `.natvis` file should exist in the installed package, or
  - The `.natvis` file should not be added to `INTERFACE_SOURCES`, or
  - It should be conditionally added only for MSVC / Visual Studio generators.

## Actual Behavior

CMake fails with:

```

Cannot find source file:
.../vcpkg_installed/x64-windows/fkYAML.natvis

```

The failure occurs during configure, before any build step.

## Notes

This repository exists solely to provide a minimal, self-contained
reproduction case.
```

---

That’s minimal, precise and maintainer-friendly.
