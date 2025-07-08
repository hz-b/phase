cmake_minimum_required(VERSION 3.10)
project(hdf5tools LANGUAGES C)

# --- HDF5 suchen (optional, falls du HDF5 nutzt) ---
find_package(HDF5 COMPONENTS C REQUIRED)
include_directories(${HDF5_INCLUDE_DIRS})

# --- Präprozessor- und Compiler-Flags ---
add_definitions(-DLINUX -DNOGUI)
add_compile_options(-I../phase -W)

# --- Gemeinsame Libraries/Quellen ---
set(PHASE_COMMON
    ../phase/error.c
    ../phase/xmalloc.c
    ../phase/xstrdup.c
)

# --- Programme und deren Quellen ---
add_executable(txtlog2phase_hdf5
    txtlog2phase_hdf5.c
    ${PHASE_COMMON}
    ../phase/myhdf5.c
)

add_executable(add_phaseu_2h5
    add_phaseu_2h5.c
    ${PHASE_COMMON}
    ../phase/myhdf5.c
    ../phaseqt/unwrap_phase.cpp
)

add_executable(wave2phase_hdf5
    wave2phase_hdf5.c
    ${PHASE_COMMON}
    ../phase/myhdf5.c
)

add_executable(genesis2phase_hdf5
    genesis2phase_hdf5.c
    ${PHASE_COMMON}
)

add_executable(wave2genesis_hdf5
    wave2genesis_hdf5.c
    ${PHASE_COMMON}
)

add_executable(genesis_hdf5_2wave
    genesis_hdf5_2wave.c
    ${PHASE_COMMON}
)

add_executable(phase_hdf5_2wave
    phase_hdf5_2wave.c
    ${PHASE_COMMON}
    ../phase/myhdf5.c
)

# --- Linker-Flags und Bibliotheken ---
set(EXTRA_LIBS m z) # -lm -lz
if(HDF5_FOUND)
    list(APPEND EXTRA_LIBS ${HDF5_LIBRARIES})
endif()

foreach(prog
    txtlog2phase_hdf5
    add_phaseu_2h5
    wave2phase_hdf5
    genesis2phase_hdf5
    wave2genesis_hdf5
    genesis_hdf5_2wave
    phase_hdf5_2wave
)
    target_link_libraries(${prog} PRIVATE ${EXTRA_LIBS})
endforeach()

# --- Installation (optional) ---
# install(TARGETS txtlog2phase_hdf5 add_phaseu_2h5 wave2phase_hdf5 genesis2phase_hdf5
#     wave2genesis_hdf5 genesis_hdf5_2wave phase_hdf5_2wave
#     RUNTIME DESTINATION bin
# )

# --- Extra Distribution (README etc.) ---
# install(FILES ../phase/phase_struct.F README DESTINATION share/hdf5tools)

# --- Clean-Regeln (CMake macht das automatisch für .o/.mod etc.) ---

# --- Hinweise ---
# - Die HDF5-Integration funktioniert am besten, wenn HDF5 korrekt installiert ist.
# - Passe ggf. die Pfade zu den Quellen an, falls die Struktur sich ändert.
# - Falls du weitere Bibliotheken brauchst, ergänze sie in EXTRA_LIBS.
