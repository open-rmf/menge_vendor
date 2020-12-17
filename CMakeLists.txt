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
  add_compile_options(-Wall -Wextra -Wpedantic)
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
)

add_library(
	menge
	SHARED
	${source_files}
)

file(
  GLOB_RECURSE
  tinyxml_sources
  ${MENGE_SRC_DIR}/tinyxml/*.cpp
)

add_library(
  tinyxml
	SHARED
	${tinyxml_sources}
)

target_link_libraries ( menge PUBLIC dl PRIVATE tinyxml )

set_target_properties( menge PROPERTIES LINKER_LANGUAGE CXX )

include(GNUInstallDirs)

ament_export_include_directories(include)

ament_export_targets(menge HAS_LIBRARY_TARGET)
ament_export_targets(tinyxml HAS_LIBRARY_TARGET)

INSTALL(
  TARGETS menge
  EXPORT menge
  DESTINATION lib
)

INSTALL(
  DIRECTORY
    ${MENGE_SRC_DIR}/MengeCore
    ${MENGE_SRC_DIR}/../include/
    ${MENGE_SRC_DIR}/tinyxml
  DESTINATION include
  FILES_MATCHING PATTERN *.h
)

INSTALL(
  TARGETS tinyxml
  EXPORT tinyxml
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib 
)

ament_package()
