# PHASE

PHASE is a physical optics and ray-tracing package.

## Build System

This repository now uses **CMake**.

## Requirements

- CMake >= 3.20
- C compiler (e.g. clang/gcc)
- C++ compiler with C++17 support
- Fortran compiler
  - Recommended: `gfortran`
  - `flang` is known to be problematic for legacy Fortran sources used by `phaseqt`/`phasesrv`

Dependency requirements depend on enabled modules:

- Always for current default build:
  - HDF5 (C + HL)
- For `phaseqt` / `phasesrv` / `opti`:
  - FFTW3
- For `phaseqt`:
  - Qt (Qt6 preferred, Qt5 supported)
  - Qwt (if `USE_QWT=ON`)
- For `opti`:
  - ROOT with Minuit

## Quick Start (Full Build)

```bash
cmake -S . -B build \
  -DCMAKE_Fortran_COMPILER=gfortran
cmake --build build -j
```

## Install

```bash
cmake --install build
```

Custom install prefix:

```bash
cmake -S . -B build \
  -DCMAKE_Fortran_COMPILER=gfortran \
  -DCMAKE_INSTALL_PREFIX=/your/prefix
cmake --build build -j
cmake --install build
```

## Main Build Options

```text
USE_MPI         (default: OFF)
USE_DEBUG       (default: OFF)
USE_OPENMP      (default: OFF)
USE_QT6         (default: ON)
USE_QWT         (default: ON)

BUILD_PHASEQT   (default: ON)
BUILD_PHASESRV  (default: ON)
BUILD_HDF5TOOLS (default: ON)
BUILD_FKOE      (default: ON)
BUILD_OPTI      (default: OFF)
BUILD_BASELIB   (default: OFF)
BUILD_PHASELIB  (default: OFF)

GRIDSIZE        (default: 1)
```

## Typical Build Variants

### 1) Full desktop/headless toolchain (recommended)

```bash
cmake -S . -B build \
  -DCMAKE_Fortran_COMPILER=gfortran \
  -DBUILD_OPTI=ON
cmake --build build -j
```

### 2) Headless/minimal tools only

Builds `hdf5tools` + `fkoe`, skips GUI/server/opti:

```bash
cmake -S . -B build-min \
  -DBUILD_PHASEQT=OFF \
  -DBUILD_PHASESRV=OFF \
  -DBUILD_OPTI=OFF
cmake --build build-min -j
```

### 3) Build a single target

```bash
cmake -S . -B build \
  -DCMAKE_Fortran_COMPILER=gfortran \
  -DBUILD_OPTI=ON
cmake --build build --target phaseqt -j
cmake --build build --target phasesrv -j
cmake --build build --target phaseopti -j
```

Other useful targets include:

- `txtlog2phase_hdf5`
- `add_phaseu_2h5`
- `wave2phase_hdf5`
- `genesis2phase_hdf5`
- `wave2genesis_hdf5`
- `genesis_hdf5_2wave`
- `phase_hdf5_2wave`
- `fkoe`
- `opti_root_example`

## Notes

- If CMake picks `flang` automatically and `phaseqt`/`phasesrv` fail, reconfigure with:

```bash
cmake -S . -B build -DCMAKE_Fortran_COMPILER=gfortran
```

- If Qt6 is installed in your home directory (for example on Ubuntu), pass its prefix explicitly:

```bash
cmake -S . -B build \
  -DCMAKE_Fortran_COMPILER=gfortran \
  -DPHASE_QT_PREFIX=$HOME/Qt/6.7.2/gcc_64
```

  Alternative (path to config directory):

```bash
cmake -S . -B build \
  -DCMAKE_Fortran_COMPILER=gfortran \
  -DQt6_DIR=$HOME/Qt/6.7.2/gcc_64/lib/cmake/Qt6
```

- Existing legacy source warnings may still appear during compilation; this is expected for now.

## Linux Runtime Note (Qt)

If `phaseqt` fails with a message like
`libQt6PrintSupport.so.6: cannot open shared object file`, either:

1. configure with `-DPHASE_QT_PREFIX=/path/to/Qt/<version>/<kit>` and reinstall (recommended), or
2. set runtime lookup path manually:

```bash
export LD_LIBRARY_PATH=/path/to/Qt/<version>/<kit>/lib:$LD_LIBRARY_PATH
```
