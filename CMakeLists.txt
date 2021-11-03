# Make sure the swig package is loaded.
find_package(SWIG REQUIRED)
include(${SWIG_USE_FILE})
if (SWIG_FOUND)
  message(STATUS "Swig version is ${SWIG_VERSION}")
else()
  message(FATAL_ERROR "Swig not found!\n")
endif()

find_package (Python COMPONENTS Interpreter Development)
if (Python_FOUND)
  message(STATUS "Python header found at ${Python_INCLUDE_DIR}")
  message(STATUS "Python library found at ${Python_LIBRARY}")
  include_directories(${Python_INCLUDE_DIR})
else()
  message(FATAL_ERROR "Python library not found!\n")
endif()

message(STATUS "Python packages ${Python_SITELIB}")

set_source_files_properties(
${PROJECT_SOURCE_DIR}/python/fmm.i PROPERTIES CPLUSPLUS ON)

if (${CMAKE_VERSION} VERSION_LESS 3.13.0)
  message(STATUS "Using swig add module")
  SWIG_ADD_LIBRARY(fmm python ${PROJECT_SOURCE_DIR}/python/fmm.i)
  swig_link_libraries(fmm FMMLIB ${Python_LIBRARY})
  install(TARGETS ${SWIG_MODULE_fmm_REAL_NAME} DESTINATION ${Python_SITELIB})
else()
  message(STATUS "Using swig add library")
  SWIG_ADD_LIBRARY(pyfmm
    LANGUAGE python
    SOURCES ${PROJECT_SOURCE_DIR}/python/fmm.i)
  set_property(TARGET pyfmm PROPERTY OUTPUT_NAME fmm)
  swig_link_libraries(pyfmm FMMLIB ${Python_LIBRARY})
  install(TARGETS pyfmm DESTINATION ${Python_SITELIB})
endif()

install(FILES ${CMAKE_BINARY_DIR}/python/fmm.py DESTINATION ${Python_SITELIB})
