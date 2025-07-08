cmake_minimum_required(VERSION 3.16)
project(phaseqt LANGUAGES C CXX Fortran)

# ---- Qt und QWT finden ----
option(USE_QT6 "Build with Qt6" OFF)
option(USE_QT5 "Build with Qt5" ON)
option(USE_QWT "Build with QWT" OFF)

if(USE_QT6)
    find_package(Qt6 COMPONENTS Core Gui Widgets Svg PrintSupport Concurrent OpenGL REQUIRED)
    set(QT_LIBS Qt6::Core Qt6::Gui Qt6::Widgets Qt6::Svg Qt6::PrintSupport Qt6::Concurrent Qt6::OpenGL)
elseif(USE_QT5)
    find_package(Qt5 COMPONENTS Core Gui Widgets Svg PrintSupport Concurrent OpenGL REQUIRED)
    set(QT_LIBS Qt5::Core Qt5::Gui Qt5::Widgets Qt5::Svg Qt5::PrintSupport Qt5::Concurrent Qt5::OpenGL)
else()
    find_package(Qt5 COMPONENTS Core Gui Widgets Svg REQUIRED)
    set(QT_LIBS Qt5::Core Qt5::Gui Qt5::Widgets Qt5::Svg)
endif()

list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/../../cmake/Modules")

message(STATUS "CMAKE_MODULE_PATH = ${CMAKE_MODULE_PATH}")

if(USE_QWT)
    find_package(Qwt REQUIRED)
endif()

# ---- Include-Verzeichnisse ----
include_directories(
    ${CMAKE_CURRENT_SOURCE_DIR}
    ../phase
    # ggf. weitere Include-Pfade wie QWT hinzufügen
)

# ---- Quellen ----
set(PHASEQT_SOURCES
    e2wavelength.cpp
    configwindow.cpp
    main.cpp
    mainwindow_slots.cpp
    mainwindow.cpp
    myzoomer.cpp
    optiinput.cpp
    phaseqt.cpp
    plot.cpp
    plot1d.cpp
    singleray.cpp
    treemodel.cpp
    treeitem.cpp
    plotmatrix.cpp
    plot2x2.cpp
    unwrap_phase.cpp
    ../phase/bline.c
    ../phase/cutils.c
    ../phase/error.c
    ../phase/phasec.c
    ../phase/OptiIntParam.c
    ../phase/rtrace.c
    ../phase/myhdf5.c
    ../phase/myfftw3.c
    ../phase/xmalloc.c
    ../phase/pst_thread.c
    ../phase/drift_8.for
    ../phase/elli_8.F
    ../phase/fdet_8.for
    ../phase/fgmapidp_8.F
    ../phase/make_matrix_8.for
    ../phase/rekursiv_2d_8.F
    ../phase/phasefor.F
    ../phase/get_partial_etc.for
    ../phase/replace_6v4v.F
    ../phase/replace_wl_in_u.F
    ../phase/replace_wl_in_ypzp.F
    ../phase/taylor-ops.for
    ../phase/pst.c
    ../phase/phase_integration.F
    ../phase/posrc.c
    ../phase/spa_3rd_order.c
    ../phase/reflectivity.c
    ../phase/heighterror.c
    ../phase/psdi.F
    ../phase/src4.F
)

set(PHASEQT_HEADERS
    e2wavelength.h
    configwindow.h
    mainwindow.h
    myzoomer.h
    optiinput.h
    phaseqt.h
    plot.h
    plot1d.h
    singleray.h
    treemodel.h
    treeitem.h
    plotmatrix.h
    plot2x2.h
    unwrap_phase.h
    ../phase/myhdf5.h
    ../phase/myfftw3.h
    ../phase/pst.h
    ../phase/posrc.h
    ../phase/spa_3rd_order.h
    ../phase/reflectivity.h
    ../phase/heighterror.h
)

# ---- Qt MOC und Ressourcen ----
set(PHASEQT_MOC_HEADERS
    configwindow.h
    mainwindow.h
    optiinput.h
    plot.h
    singleray.h
    treemodel.h
    treeitem.h
    plotmatrix.h
    plot2x2.h
    e2wavelength.h
)

set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)


# ---- Executable ----
add_executable(phaseqt
    ${PHASEQT_SOURCES}
    ${PHASEQT_HEADERS}
    ${PHASEQT_MOC_SRCS}
    ${PHASEQT_RCC_SRCS}
)

# ---- Compiler- und Präprozessor-Flags ----
target_compile_definitions(phaseqt PRIVATE LINUX QTGUI QT_GUI_LIB QT_CORE_LIB QT_SHARED)
if(USE_QT6)
    target_compile_definitions(phaseqt PRIVATE QT6)
elseif(USE_QT5)
    target_compile_definitions(phaseqt PRIVATE QT5)
endif()

target_compile_options(phaseqt PRIVATE -fPIC)

# ---- Linker-Flags und Bibliotheken ----
target_link_libraries(phaseqt PRIVATE ${QT_LIBS})
#if(USE_QWT)
#    target_link_libraries(phaseqt PRIVATE Qwt::Qwt)
#endif()
# Weitere Bibliotheken (HDF5, FFTW3, eigene Fortran-Libs etc.) hier ergänzen

# ---- Daten installieren ----
install(TARGETS phaseqt RUNTIME DESTINATION bin)
install(FILES
    ../data/coefficient-example.coeff
    ../data/map70_lh.omx
    ../data/map70_rh.omx
    ../data/spa3table.tab
    ../data/spa3table_20001.tab
    ../data/material/rhoatom.dat
    ../data/henke/Ac.f12 ../data/henke/Ag.f12 ../data/henke/Al.f12
    ../data/henke/Ar.f12 ../data/henke/As.f12 ../data/henke/At.f12
    ../data/henke/Au.f12 ../data/henke/Ba.f12 ../data/henke/Be.f12
    ../data/henke/B.f12 ../data/henke/Bi.f12 ../data/henke/Br.f12
    ../data/henke/Ca.f12 ../data/henke/Cd.f12 ../data/henke/Ce.f12
    ../data/henke/C.f12 ../data/henke/Cl.f12 ../data/henke/Co.f12
    ../data/henke/Cr.f12 ../data/henke/Cs.f12 ../data/henke/Cu.f12
    ../data/henke/Dy.f12 ../data/henke/Er.f12 ../data/henke/Eu.f12
    ../data/henke/Fe.f12 ../data/henke/F.f12 ../data/henke/Fr.f12
    ../data/henke/Ga.f12 ../data/henke/Gd.f12 ../data/henke/Ge.f12
    ../data/henke/He.f12 ../data/henke/H.f12 ../data/henke/Hf.f12
    ../data/henke/Hg.f12 ../data/henke/Ho.f12 ../data/henke/I.f12
    ../data/henke/In.f12 ../data/henke/Ir.f12 ../data/henke/K.f12
    ../data/henke/Kr.f12 ../data/henke/La.f12 ../data/henke/Li.f12
    ../data/henke/Lu.f12 ../data/henke/Mg.f12 ../data/henke/Mn.f12
    ../data/henke/Mo.f12 ../data/henke/Na.f12 ../data/henke/Nb.f12
    ../data/henke/Nd.f12 ../data/henke/Ne.f12 ../data/henke/N.f12
    ../data/henke/Ni.f12 ../data/henke/O.f12 ../data/henke/Os.f12
    ../data/henke/Pa.f12 ../data/henke/Pb.f12 ../data/henke/Pd.f12
    ../data/henke/P.f12 ../data/henke/Pm.f12 ../data/henke/Po.f12
    ../data/henke/Pr.f12 ../data/henke/Pt.f12 ../data/henke/Ra.f12
    ../data/henke/Rb.f12 ../data/henke/Re.f12 ../data/henke/Rh.f12
    ../data/henke/Rn.f12 ../data/henke/Ru.f12 ../data/henke/Sb.f12
    ../data/henke/Sc.f12 ../data/henke/Se.f12 ../data/henke/S.f12
    ../data/henke/Si.f12 ../data/henke/Sm.f12 ../data/henke/Sn.f12
    ../data/henke/Sr.f12 ../data/henke/Ta.f12 ../data/henke/Tb.f12
    ../data/henke/Tc.f12 ../data/henke/Te.f12 ../data/henke/Th.f12
    ../data/henke/Ti.f12 ../data/henke/Tl.f12 ../data/henke/Tm.f12
    ../data/henke/U.f12 ../data/henke/V.f12 ../data/henke/W.f12
    ../data/henke/Xe.f12 ../data/henke/Yb.f12 ../data/henke/Y.f12
    ../data/henke/Zn.f12 ../data/henke/Zr.f12 ../data/henke/B4C.f12
    DESTINATION share/phaseqt/data
)

# ---- Extra Distribution (README, Bilder, etc.) ----
install(FILES
    phaseqt.qrc parameter.default
    images/Blue-darrow-32.png images/Blue-tarrow-32.png
    images/Blue-arrow-left-32.png images/Blue-arrow-right-32.png
    images/Blue-arrows-right-32.png images/down-32.png images/footprint.png
    images/Left-arrow-48.png images/new.png images/print.png
    images/quickrtrace.png images/Right-arrow-48.png images/rtrace.png
    images/save.png images/undo.png images/up-32.png
    README
    DESTINATION share/phaseqt
)

# ---- Clean-Regeln ----
set_directory_properties(PROPERTIES ADDITIONAL_MAKE_CLEAN_FILES "moc_*.cpp;qrc_*.cpp;*.f90;*.mod")

