project( dummy )

cmake_minimum_required( VERSION 2.8 )

find_package( Ceres REQUIRED )
include_directories( ${CERES_INCLUDES} )

add_executable( helloworld helloworld.cxx )
target_link_libraries( helloworld ${CERES_LIBRARIES} )
