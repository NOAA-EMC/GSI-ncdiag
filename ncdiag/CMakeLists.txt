list(APPEND NCDIAG_SRC_lib
nc_diag_fson.f90 nc_diag_read_mod.F90 nc_diag_res.f90 nc_diag_write_mod.F90
ncd_kinds.F90 ncdc_cli_process.F90 ncdc_climsg.F90 ncdc_data.F90 ncdc_data_MPI.F90
ncdc_dims.F90 ncdc_metadata.F90 ncdc_realloc.F90 ncdc_state.F90 ncdc_types.f90
ncdc_vars.F90 ncdf_path_m.F90 ncdf_string_m.f90 ncdf_value_m.f90 ncdr_alloc_assert.f90
ncdr_attrs.f90 ncdr_attrs_fetch.f90 ncdr_check.f90 ncdr_climsg.F90 ncdr_dims.f90
ncdr_global_attrs.f90 ncdr_global_attrs_fetch.f90 ncdr_realloc_mod.F90 ncdr_state.f90
ncdr_types.f90 ncdr_vars.f90 ncdr_vars_fetch.f90 ncdres_climsg.F90 ncdw_chaninfo.F90
ncdw_ciresize.F90 ncdw_climsg.F90 ncdw_data2d.F90 ncdw_dresize.F90 ncdw_lheader.F90
ncdw_metadata.F90 ncdw_mresize.F90 ncdw_realloc.F90 ncdw_state.f90 ncdw_strarrutils.F90
ncdw_types.F90 ncdw_varattr.F90 netcdf_unlimdims.F90)

list(APPEND NCDIAG_SRC_app
nc_diag_cat.F90 test_nc_unlimdims.F90)

set(module_dir "${CMAKE_CURRENT_BINARY_DIR}/include/ncdiag")
add_library(ncdiag STATIC ${NCDIAG_SRC_lib})
add_library(${PROJECT_NAME}::ncdiag ALIAS ncdiag)
set_target_properties(ncdiag PROPERTIES Fortran_MODULE_DIRECTORY "${module_dir}")
target_compile_definitions(ncdiag PRIVATE "_REAL4_")
target_compile_definitions(ncdiag PRIVATE "USE_MPI")
target_link_libraries(ncdiag PUBLIC NetCDF::NetCDF_Fortran)
target_link_libraries(ncdiag PUBLIC MPI::MPI_Fortran)
target_include_directories(ncdiag PUBLIC $<BUILD_INTERFACE:${module_dir}>
                                         $<INSTALL_INTERFACE:include/ncdiag>)
list(APPEND NCDIAG_Targets ncdiag)
install(DIRECTORY ${module_dir} DESTINATION ${CMAKE_INSTALL_PREFIX}/include)

# Build applications
foreach(app ${NCDIAG_SRC_app})
  get_filename_component(app_name ${app} NAME_WLE)
  add_executable(${app_name}.x ${app})
  add_dependencies(${app_name}.x ncdiag)
  target_compile_definitions(${app_name}.x PRIVATE "USE_MPI")
  target_link_libraries(${app_name}.x PUBLIC ncdiag)
  target_link_libraries(${app_name}.x PUBLIC NetCDF::NetCDF_Fortran)
  target_link_libraries(${app_name}.x PUBLIC MPI::MPI_Fortran)
  list(APPEND NCDIAG_Targets ${app_name}.x)
endforeach()

# Build serial library and applications
if(ENABLE_NCDIAG_SERIAL)
  set(module_dir "${CMAKE_CURRENT_BINARY_DIR}/include/ncdiag_serial")
  add_library(ncdiag_serial STATIC ${NCDIAG_SRC_lib})
  add_library(${PROJECT_NAME}::ncdiag_serial ALIAS ncdiag_serial)
  set_target_properties(ncdiag_serial PROPERTIES Fortran_MODULE_DIRECTORY "${module_dir}")
  target_compile_definitions(ncdiag_serial PRIVATE "_REAL4_")
  target_link_libraries(ncdiag_serial PUBLIC NetCDF::NetCDF_Fortran)
  target_include_directories(ncdiag_serial PUBLIC $<BUILD_INTERFACE:${module_dir}>
                                                  $<INSTALL_INTERFACE:include/ncdiag_serial>)
  list(APPEND NCDIAG_Targets ncdiag_serial)

  set(app_name "ncdiag_cat_serial")
  add_executable(${app_name}.x nc_diag_cat.F90)
  add_dependencies(${app_name}.x ncdiag_serial)
  target_link_libraries(${app_name}.x PUBLIC ncdiag_serial)
  target_link_libraries(${app_name}.x PUBLIC NetCDF::NetCDF_Fortran)
  list(APPEND NCDIAG_Targets ${app_name}.x)
  install(DIRECTORY ${module_dir} DESTINATION ${CMAKE_INSTALL_PREFIX}/include)
endif()

install(
  TARGETS ${NCDIAG_Targets}
  EXPORT ${PROJECT_NAME}Exports
  RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
  ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR})

# Package config
include(CMakePackageConfigHelpers)
set(CONFIG_INSTALL_DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME})

export(EXPORT ${PROJECT_NAME}Exports
       NAMESPACE ${PROJECT_NAME}::
       FILE ${PROJECT_NAME}-targets.cmake)

configure_package_config_file(
  ${PROJECT_SOURCE_DIR}/cmake/PackageConfig.cmake.in ${CMAKE_BINARY_DIR}/${PROJECT_NAME}-config.cmake
  INSTALL_DESTINATION ${CONFIG_INSTALL_DESTINATION})
install(FILES ${CMAKE_BINARY_DIR}/${PROJECT_NAME}-config.cmake
        DESTINATION ${CONFIG_INSTALL_DESTINATION})

write_basic_package_version_file(
  ${CMAKE_BINARY_DIR}/${PROJECT_NAME}-config-version.cmake
  VERSION ${PROJECT_VERSION}
  COMPATIBILITY AnyNewerVersion)
install(FILES ${CMAKE_BINARY_DIR}/${PROJECT_NAME}-config-version.cmake
        DESTINATION ${CONFIG_INSTALL_DESTINATION})

install(EXPORT ${PROJECT_NAME}Exports
        NAMESPACE ${PROJECT_NAME}::
        FILE ${PROJECT_NAME}-targets.cmake
        DESTINATION ${CONFIG_INSTALL_DESTINATION})
