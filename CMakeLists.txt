cmake_minimum_required(VERSION 3.20)
project(phase
  VERSION 1.33
  DESCRIPTION "PHASE Simulation Software"
  LANGUAGES C CXX Fortran
)
# Setze die CMake-Module-Pfade
list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake/Modules")

# Optionen und Einstellungen
option(USE_MPI "Enable MPI support" AUTO)
option(USE_DEBUG "Enable debug build" OFF)
option(USE_EXPERIMENTAL "Enable experimental features" OFF)
option(USE_F2C "Enable F2C compatibility" OFF)
option(USE_PHASELIB "Use legacy PHASELIB" OFF)
option(USE_OPENMP "Enable OpenMP" OFF)
option(USE_QT6 "Prefer Qt6 over Qt5" ON)
option(USE_QWT "Enable QWT support" ON)

set(GRIDSIZE 1 CACHE STRING "Max grid size for PO source")

# Compiler-Einstellungen
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

if(USE_DEBUG)
  add_compile_definitions(DEBUG)
  set(CMAKE_BUILD_TYPE Debug)
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -ggdb")
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -ggdb")
else()
  set(CMAKE_BUILD_TYPE Release)
endif()

# Compiler-spezifische Einstellungen
if(CMAKE_Fortran_COMPILER_ID MATCHES "Intel")
  add_compile_definitions(HAVE_INTEL_F77)
  set(Fortran_FLAGS "-DINTEL -132 -nogen-interfaces -threads -auto -noalign")
elseif(CMAKE_Fortran_COMPILER_ID MATCHES "GNU")
  add_compile_definitions(HAVE_GFORTRAN_F77)
  set(Fortran_FLAGS "--std=legacy -DGNU")
endif()

# MPI Unterstützung
if(USE_MPI)
  find_package(MPI REQUIRED)
  add_compile_definitions(USE_MPI)
endif()

# Qt Unterstützung
if(USE_QT6)
  find_package(Qt6 COMPONENTS Core Gui Widgets Svg LinguistTools REQUIRED)
else()
  find_package(Qt5 5.9 COMPONENTS Core Gui Widgets Svg LinguistTools REQUIRED)
endif()

# QWT Unterstützung
if(USE_QWT)
  find_package(Qwt REQUIRED)
  add_compile_definitions(HAVE_QWT)
endif()

# HDF5 Unterstützung
find_package(HDF5 REQUIRED COMPONENTS C HL)

# FFTW3 Unterstützung
find_package(FFTW3 REQUIRED)

# ROOT Framework
find_package(ROOT REQUIRED)

# OpenMP
if(USE_OPENMP)
  find_package(OpenMP REQUIRED)
  add_compile_definitions(OPENMP)
endif()

# Systemchecks
if(CMAKE_SYSTEM_PROCESSOR MATCHES "x86_64")
  add_compile_definitions(HAVE_X86_64)
endif()

# Installationspfade
set(CMAKE_INSTALL_PREFIX "/usr/local" CACHE PATH "Installation directory")
set(PHASE_LIB_DIR "${CMAKE_INSTALL_PREFIX}/lib/phase")

# Unterverzeichnisse
add_subdirectory(src/baselib)
add_subdirectory(src/phaseqt)
add_subdirectory(src/phaselib)
add_subdirectory(src/opti)
add_subdirectory(src/hdf5tools)

# Bibliotheken
#add_library(phase_common STATIC)
#target_sources(phase_common PRIVATE
#  # Quellendateien hier einfügen
#)

#target_include_directories(phase_common PUBLIC
#  ${CMAKE_CURRENT_SOURCE_DIR}/include
#  ${HDF5_INCLUDE_DIRS}
#  ${FFTW3_INCLUDE_DIRS}
#  ${ROOT_INCLUDE_DIRS}
#)

#target_link_libraries(phase_common PUBLIC
#  ${HDF5_LIBRARIES}
#  ${FFTW3_LIBRARIES}
#  ${ROOT_LIBRARIES}
#)

# Executables
#add_executable(phase_main main.cpp)
#target_link_libraries(phase_main PRIVATE
#  phase_common
#  Qt::Core
#  Qt::Gui
#  ${QWT_LIBRARIES}
#)

#if(USE_MPI)
#  target_link_libraries(phase_main PRIVATE MPI::MPI_CXX)
#endif()

# Installation
#install(TARGETS phase_main DESTINATION bin)
#install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/idl DESTINATION share/phase)
#install(FILES config.h DESTINATION include/phase)

# Benutzerdefinierte Meldungen
message(STATUS "Build configuration summary:")
message(STATUS "  Install prefix:      ${CMAKE_INSTALL_PREFIX}")
message(STATUS "  Build type:          ${CMAKE_BUILD_TYPE}")
message(STATUS "  MPI support:         ${USE_MPI}")
message(STATUS "  Qt version:          ${Qt_VERSION}")
message(STATUS "  QWT support:         ${USE_QWT}")
message(STATUS "  HDF5 version:        ${HDF5_VERSION}")
message(STATUS "  FFTW3 support:       ${FFTW3_FOUND}")
message(STATUS "  ROOT version:        ${ROOT_VERSION}")
message(STATUS "  OpenMP support:      ${USE_OPENMP}")