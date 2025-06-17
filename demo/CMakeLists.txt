cmake_minimum_required(VERSION 3.11)
project(demo)

set(CMAKE_CXX_STANDARD 20)
set_property(GLOBAL PROPERTY USE_FOLDERS ON)
set_property(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR} PROPERTY VS_STARTUP_PROJECT demo)

include(FetchContent)

# Fetch FreeType
FetchContent_Declare(
    freetype
    GIT_REPOSITORY https://github.com/freetype/freetype.git
    GIT_TAG        VER-2-13-3
)
if(POLICY CMP0077)
  cmake_policy(SET CMP0077 NEW) # Squish CMake deprecation warning
endif()
FetchContent_MakeAvailable(freetype)

# Fetch HarfBuzz
FetchContent_Declare(
    harfbuzz
    GIT_REPOSITORY https://github.com/harfbuzz/harfbuzz.git
    GIT_TAG        11.2.1
)
set(HB_BUILD_SUBSET OFF CACHE BOOL "" FORCE)
FetchContent_MakeAvailable(harfbuzz)

# Gather sources
file(GLOB SOURCES "*.cpp" "*.hpp" "*.h" "include/*.h")

# Add executable
add_executable(demo ${SOURCES})

set_target_properties(demo PROPERTIES
    VS_DEBUGGER_WORKING_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}"
)

# Enable UTF-8 source encoding for MSVC
if(MSVC)
    target_compile_options(demo PRIVATE /utf-8)
endif()

# Include directories
target_include_directories(demo
    PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}/include
        ${CMAKE_CURRENT_SOURCE_DIR}/src
        ${freetype_SOURCE_DIR}/include
        ${harfbuzz_SOURCE_DIR}/src
)

# Link libraries
target_link_libraries(demo PRIVATE freetype harfbuzz glu32)

