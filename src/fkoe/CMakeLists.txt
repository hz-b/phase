add_executable(fkoe
  spa_check.F
  spa-check.cmn
  write_phase_hdf5.c
  ../phase/myhdf5.c
  ../phase/myhdf5.h
  mytime.c
  ../phase/xmalloc.c
  ../phase/xstrdup.c
  ../phase/error.c
)

target_compile_definitions(fkoe PRIVATE NOGUI)
target_link_libraries(fkoe PRIVATE phase_common_flags phase_hdf5)

install(TARGETS fkoe RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR})
install(FILES fkoe.par gauss_beam.par mk_surf.par DESTINATION ${CMAKE_INSTALL_DATADIR}/fkoe)
