cmake_minimum_required(VERSION 2.8)
project(libco)

# This for mac osx only
set(CMAKE_MACOSX_RPATH 0)

# Set lib version
set(LIBCO_VERSION   0.5)

# Set cflags
set(CMAKE_C_FLAGS ${CMAKE_C_FLAGS} -g -fno-strict-aliasing -O2 -Wall -export-dynamic -Wall -pipe  -D_GNU_SOURCE -D_REENTRANT -fPIC -Wno-deprecated -m64)

# Use c and asm
enable_language(C ASM)

# Add source files
FILE (GLOB ALL_SOURCES "src/*.cpp" "src/*.S") 
SET (SOURCE_FILES ${ALL_SOURCES})  

# Add static and shared library target
add_library(colib_static STATIC ${SOURCE_FILES})
add_library(colib_shared SHARED ${SOURCE_FILES})

# Set library output name
set_target_properties(colib_static PROPERTIES OUTPUT_NAME colib)
set_target_properties(colib_shared PROPERTIES OUTPUT_NAME colib)

set_target_properties(colib_static PROPERTIES CLEAN_DIRECT_OUTPUT 1)
set_target_properties(colib_shared PROPERTIES CLEAN_DIRECT_OUTPUT 1)

# Set shared library version, will generate libcolib.${LIBCO_VERSION}.so and a symbol link named libcolib.so
# For mac osx, the extension name will be .dylib
set_target_properties(colib_shared PROPERTIES VERSION ${LIBCO_VERSION} SOVERSION ${LIBCO_VERSION})

## Add test targets
include_directories(src) 

file(GLOB_RECURSE CPP_SRC_LIST test/*.cpp)
foreach(v ${CPP_SRC_LIST}) 
    STRING(REGEX REPLACE  ".+/(.+)\\.cpp"  "\\1" FILE_NAME ${v}) 
    message(STATUS "=== BUILDING === ${v}") 
    add_executable(${FILE_NAME} ${v})
    target_link_libraries(${FILE_NAME} colib_static pthread dl) 
endforeach() 


