cmake_minimum_required( VERSION 2.8.3 )

project( SuperBuild )

set( INSTALL_DIR ${CMAKE_BINARY_DIR}/INSTALL
  CACHE PATH "Install directory"
)

include( ExternalProject )

# Compute -G arg for configuring external projects with the same CMake
# generator:
if(CMAKE_EXTRA_GENERATOR)
  set(gen "${CMAKE_EXTRA_GENERATOR} - ${CMAKE_GENERATOR}")
else()
  set(gen "${CMAKE_GENERATOR}" )
endif()

set(ep_common_args
  -DCMAKE_C_COMPILER:FILEPATH=${CMAKE_C_COMPILER}
  -DCMAKE_CXX_COMPILER:FILEPATH=${CMAKE_CXX_COMPILER}
)

if(${CMAKE_BUILD_TYPE})
  set(ep_common_args ${ep_common_args}
    -DCMAKE_BUILD_TYPE:STRING=${CMAKE_BUILD_TYPE}
  )
endif()

set( MySuperProject_DEPENDENCIES )

ExternalProject_Add( CeresSolver
  # DEPENDS  # Here add dependencies if any
  GIT_REPOSITORY https://ceres-solver.googlesource.com/ceres-solver
  GIT_TAG 1.5.0
  SOURCE_DIR ceres-solver
  BINARY_DIR ceres-solver-build
  UPDATE_COMMAND ""
  PATCH_COMMAND ""
  CMAKE_GENERATOR ${gen}
  CMAKE_ARGS
    ${ep_common_args}
    -DBUILD_DOCUMENTATION:BOOL=OFF
    -DBUILD_EXAMPLES:BOOL=OFF
    -DBUILD_TESTING:BOOL=OFF
    -DBUILD_ANDROID:BOOL=OFF
    -DCMAKE_INSTALL_PREFIX:PATH=${INSTALL_DIR}
)

set( MySuperProject_DEPENDENCIES ${MySuperProject_DEPENDENCIES} CeresSolver )

#---------------------------------------------------------------------------------------------
ExternalProject_Add( MySuperProject
  DEPENDS ${MySuperProject_DEPENDENCIES}
  DOWNLOAD_COMMAND ""
	UPDATE_COMMAND ""
	PATCH_COMMAND ""
  INSTALL_COMMAND ""
	SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/..
	BINARY_DIR MySuperProject-build
	CMAKE_GENERATOR ${gen}
	CMAKE_ARGS
	  ${ep_common_args}
    -DBUILD_DEPENDENCIES:BOOL=OFF
    -DCeres_DIR:PATH=${INSTALL_DIR}/lib/cmake/Ceres
)
