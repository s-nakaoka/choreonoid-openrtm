set(IS_MASTER_PROJECT OFF)
if(CMAKE_CURRENT_SOURCE_DIR STREQUAL CMAKE_SOURCE_DIR)
  set(IS_MASTER_PROJECT ON)
endif()

if(NOT IS_MASTER_PROJECT)
  option(BUILD_OPENRTM_PLUGIN "Building OpenRTMPlugin" OFF)
  if(NOT BUILD_OPENRTM_PLUGIN)
    return()
  elseif(NOT (ENABLE_CORBA AND BUILD_CORBA_PLUGIN))
    message(FATAL_ERROR "The OpenRTM plugin needs to ENABLE_CORBA and BUILD_CORBA_PLUGIN")
  endif()

else()
  cmake_minimum_required(VERSION 3.5.0)
  project(ChoreonoidOpenRTM)

  if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release CACHE STRING
      "Choose the type of build, options are: None Debug Release RelWithDebInfo MinSizeRel."
      FORCE)
  endif()

  if(NOT Choreonoid_FOUND)
    file(GLOB choreonoid_search_path
      "c:/choreonoid*/share/choreonoid/cmake"
      "c:/choreonoid*/*/share/choreonoid/cmake")
  endif()
  if(choreonoid_search_path)
    find_package(Choreonoid REQUIRED HINTS ${choreonoid_search_path})
  else()
    find_package(Choreonoid REQUIRED)
  endif()
  
  if(NOT CHOREONOID_ENABLE_CORBA)
    message(FATAL_ERROR "CORBA is not available in Choreonoid")
  endif()
  
  if(CHOREONOID_CXX_STANDARD)
    set(CMAKE_CXX_STANDARD ${CHOREONOID_CXX_STANDARD})
    set(CMAKE_CXX_EXTENSIONS OFF)
  else()
    add_compile_options(-std=c++14)
  endif()

  option(INSTALL_SDK "Installing SDK files" ON)
  set(CHOREONOID_INSTALL_SDK ${INSTALL_SDK})

  include_directories(${PROJECT_SOURCE_DIR})
  include_directories(${PROJECT_BINARY_DIR}/include)

  include_directories(${CHOREONOID_INCLUDE_DIRS})
  link_directories(${CHOREONOID_LIBRARY_DIRS})
  set_property(DIRECTORY APPEND PROPERTY COMPILE_DEFINITIONS ${CHOREONOID_COMPILE_DEFINITIONS})
endif()

find_package(OpenRTM REQUIRED CONFIG)

# Check version
if(NOT OPENRTM_VERSION VERSION_LESS "1.2.0")
  list(APPEND OPENRTM_DEFINITIONS -DOPENRTM_VERSION12)
  if(UNIX)
    list(APPEND OPENRTM_DEFINITIONS -Wno-deprecated)
  endif()

elseif(OPENRTM_VERSION VERSION_EQUAL "1.1.2")
  list(APPEND OPENRTM_DEFINITIONS -DOPENRTM_VERSION11)
  # Remove extra "-I" from the elements of OPENRTM_INCLUDE_DIRS
  unset(include_dirs)
  foreach(dir ${OPENRTM_INCLUDE_DIRS})
    string(REGEX REPLACE "^-I" "" stripped ${dir})
    list(APPEND include_dirs ${stripped})
  endforeach()
  set(OPENRTM_INCLUDE_DIRS ${include_dirs})
  # Remove extra "-L" from the elements of OPENRTM_LIBRARY_DIRS
  unset(library_dirs)
  foreach(dir ${OPENRTM_LIBRARY_DIRS})
    string(REGEX REPLACE "^-L" "" stripped ${dir})
    list(APPEND library_dirs ${stripped})
  endforeach()
  set(OPENRTM_LIBRARY_DIRS ${library_dirs})

else()
  message(FATAL_ERROR "OpenRTM version is INVALID.")
endif()

add_definitions(${OPENRTM_DEFINITIONS})
include_directories(${OPENRTM_INCLUDE_DIRS})
link_directories(${OPENRTM_LIBRARY_DIRS})

if(MSVC)
  add_compile_options(${OPENRTM_CFLAGS})
endif()

set(IDL_INCLUDE_DIRS ${IDL_INCLUDE_DIRS} ${OPENRTM_INCLUDE_DIRS})

option(USE_BUILTIN_CAMERA_IMAGE_IDL "Enable this option when you run hrpsys-base on Choreonoid" OFF)
if(USE_BUILTIN_CAMERA_IMAGE_IDL)
  list(APPEND OPENRTM_DEFINITIONS -DUSE_BUILTIN_CAMERA_IMAGE_IDL)
else()
  if(MSVC)
    unset(camera_library)
    foreach(dir ${OPENRTM_LIBRARY_DIRS})
      file(GLOB libs "${dir}/rtmCamera*.lib")
      foreach(lib ${libs})
        if(lib MATCHES "(rtmCamera.*d)\\.lib$")
          list(APPEND camera_library debug;${CMAKE_MATCH_1})
        elseif(lib MATCHES "(rtmCamera.*)\\.lib$")
          list(APPEND camera_library optimized;${CMAKE_MATCH_1})
        endif()
      endforeach()
    endforeach()
    set(OPENRTM_CAMERA_LIBRARY ${camera_library})
  else()
    set(OPENRTM_CAMERA_LIBRARY rtmCamera)
  endif()
endif()

if(NOT IS_MASTER_PROJECT)
  set(OPENRTM_DEFINITIONS ${OPENRTM_DEFINITIONS} PARENT_SCOPE)
  set(OPENRTM_INCLUDE_DIRS ${OPENRTM_INCLUDE_DIRS} PARENT_SCOPE)
  set(OPENRTM_LIBRARY_DIRS ${OPENRTM_LIBRARY_DIRS} PARENT_SCOPE)
  set(OPENRTM_CFLAGS ${OPENRTM_CFLAGS} PARENT_SCOPE)
  set(OPENRTM_CAMERA_LIBRARY ${OPENRTM_CAMERA_LIBRARY} PARENT_SCOPE)
  set(IDL_INCLUDE_DIRS ${IDL_INCLUDE_DIRS} PARENT_SCOPE)
endif()

add_subdirectory(src)
add_subdirectory(sample)
