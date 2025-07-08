cmake_minimum_required(VERSION 3.10)
project(phaselib_tools LANGUAGES C CXX Fortran)

# --- Compiler- und Präprozessor-Flags ---
add_definitions(-DLINUX -DOPTI -DNOGUI)

# Falls du spezielle ROOT-Flags und Libraries hast, trage sie hier ein:
# Beispiel (ersetze ggf. durch find_package(ROOT ...) und target_link_libraries):
set(ROOT_CFLAGS "")  # z.B. -I/usr/include/root
set(ROOT_LIBS "")    # z.B. -L/usr/lib/root -lCore -lHist -lGpad
set(MY_HDF5_LIBS "") # ggf. durch find_package(HDF5 ...) ersetzen
set(FLIBS "")        # ggf. Fortran-Libs
set(CFORLIB "")      # ggf. eigene Fortran-Libs

include_directories(
    .
    ../phase
)

# --- Executable: phaseopti ---
add_executable(phaseopti
    phaseopti.cpp phaseopti.h
    mycost.cpp mycost.h
    optisubc.c optisubc.h
    ../phase/xmalloc.c
    ../phase/fgmapidp_8.F
    ../phase/make_matrix_8.for
    ../phase/fdet_8.for
    ../phase/taylor-ops.for
    ../phase/rekursiv_2d_8.F
    ../phase/get_partial_etc.for
    ../phase/replace_6v4v.F
    ../phase/replace_wl_in_u.F
    ../phase/replace_wl_in_ypzp.F
    ../phase/drift_8.for
    ../phase/elli_8.F
    ../phase/phasefor.F
    ../phase/error.c
    ../phase/xstrdup.c
    ../phase/cutils.c
    ../phase/rtrace.c
    ../phase/bline.c
    ../phase/reflectivity.c ../phase/reflectivity.h
    ../phase/heighterror.c ../phase/heighterror.h
    ../phase/myhdf5.c ../phase/myhdf5.h
)

# --- Executable: opti_root_example ---
add_executable(opti_root_example
    opti_root_example.cpp
    opti_root_example.h
)

# --- Linker-Flags und Bibliotheken ---
target_compile_options(phaseopti PRIVATE ${ROOT_CFLAGS} -W)
target_link_libraries(phaseopti PRIVATE ${ROOT_LIBS} -lMinuit ${MY_HDF5_LIBS} ${FLIBS} ${CFORLIB})

# Für opti_root_example ggf. auch ROOT angeben, falls benötigt:
# target_compile_options(opti_root_example PRIVATE ${ROOT_CFLAGS})
# target_link_libraries(opti_root_example PRIVATE ${ROOT_LIBS})

# --- Hinweise ---
# - Die Variablen wie ${ROOT_CFLAGS}, ${ROOT_LIBS}, ${MY_HDF5_LIBS}, ${FLIBS}, ${CFORLIB} musst du ggf. durch echte Werte ersetzen oder mit find_package() und target_link_libraries() korrekt konfigurieren.
# - Die .h-Dateien müssen nicht unbedingt in add_executable() stehen, schaden aber nicht.
# - Wenn du Installationsregeln brauchst, ergänze z.B.:
# install(TARGETS phaseopti opti_root_example RUNTIME DESTINATION bin)