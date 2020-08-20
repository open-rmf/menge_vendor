cmake_minimum_required(VERSION 3.5)
project(menge)

# Default to C99
if(NOT CMAKE_C_STANDARD)
  set(CMAKE_C_STANDARD 99)
endif()

# to C++11
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 11)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic 
    -Wno-deprecated 
    -Wno-unused-parameter
    -Wno-unknown-pragmas
    -Wno-unused-but-set-variable
    -Wno-unused-variable
    -Wno-parentheses
    -Wno-reorder
    -Wno-endif-labels)
endif()

if(APPLE)
    set(CMAKE_MACOSX_RPATH ON)
endif()

# find dependencies
find_package(ament_cmake REQUIRED)

set(MENGE_SRC_DIR ${CMAKE_SOURCE_DIR}/src/Menge)
message("menge_source_dir " ${MENGE_SRC_DIR})

#Where to find the header files
INCLUDE_DIRECTORIES (${MENGE_SRC_DIR}/)
INCLUDE_DIRECTORIES (${MENGE_SRC_DIR}/../include)
INCLUDE_DIRECTORIES (${MENGE_SRC_DIR}/../)

set( CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -fpermissive" )

#find the correct OpenMP flag
FIND_PACKAGE(OpenMP)
if(OPENMP_FOUND)
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
    set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} ${OpenMP_CXX_FLAGS} -fpermissive -DNDEBUG")
    set(CMAKE_EXE_LINKER_FLAGS_RELEASE "${CMAKE_EXE_LINKER_FLAGS_RELEASE} ${OpenMP_EXE_LINKER_FLAGS}")
endif()


file(
	GLOB_RECURSE
	source_files
	${MENGE_SRC_DIR}/MengeCore/*.cpp
	${MENGE_SRC_DIR}/MengeCore/*.h
)

add_library(
	mengeCore
	SHARED
	${source_files}
)

target_link_libraries ( mengeCore PUBLIC dl PRIVATE tinyxml )

set_target_properties( mengeCore PROPERTIES LINKER_LANGUAGE CXX )

include(GNUInstallDirs)

ament_export_include_directories(include)
ament_export_libraries(mengeCore)

install(TARGETS mengeCore
  DESTINATION ${CMAKE_INSTALL_LIBDIR}
)

install(
  DIRECTORY
    ${MENGE_SRC_DIR}/MengeCore
    ${MENGE_SRC_DIR}/../include/
    ${MENGE_SRC_DIR}/tinyxml
  DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
  FILES_MATCHING PATTERN *.h
)


ament_package()
