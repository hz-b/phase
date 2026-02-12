set(PHASE_COMMON_SOURCES
  ../phase/error.c
  ../phase/xmalloc.c
  ../phase/xstrdup.c
)

add_executable(txtlog2phase_hdf5
  txtlog2phase_hdf5.c
  ${PHASE_COMMON_SOURCES}
  ../phase/myhdf5.c
)

add_executable(add_phaseu_2h5
  add_phaseu_2h5.c
  ${PHASE_COMMON_SOURCES}
  ../phase/myhdf5.c
  ../phaseqt/unwrap_phase.cpp
)

add_executable(wave2phase_hdf5
  wave2phase_hdf5.c
  ${PHASE_COMMON_SOURCES}
  ../phase/myhdf5.c
)

add_executable(genesis2phase_hdf5
  genesis2phase_hdf5.c
  ${PHASE_COMMON_SOURCES}
)

add_executable(wave2genesis_hdf5
  wave2genesis_hdf5.c
  ${PHASE_COMMON_SOURCES}
)

add_executable(genesis_hdf5_2wave
  genesis_hdf5_2wave.c
  ${PHASE_COMMON_SOURCES}
)

add_executable(phase_hdf5_2wave
  phase_hdf5_2wave.c
  ${PHASE_COMMON_SOURCES}
  ../phase/myhdf5.c
)

set(HDF5TOOLS_TARGETS
  txtlog2phase_hdf5
  add_phaseu_2h5
  wave2phase_hdf5
  genesis2phase_hdf5
  wave2genesis_hdf5
  genesis_hdf5_2wave
  phase_hdf5_2wave
)

foreach(prog IN LISTS HDF5TOOLS_TARGETS)
  target_compile_definitions(${prog} PRIVATE NOGUI)
  target_link_libraries(${prog} PRIVATE phase_common_flags phase_hdf5 m z)
endforeach()

install(TARGETS ${HDF5TOOLS_TARGETS} RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR})
install(FILES ../phase/phase_struct.F README DESTINATION ${CMAKE_INSTALL_DATADIR}/hdf5tools)
