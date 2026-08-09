cmake_minimum_required(VERSION 4.2)

project(implot)
set(CMAKE_CXX_STANDARD 23)
set(CMAKE_INTERPROCEDURAL_OPTIMIZATION TRUE) # Enable link-time optimizations.

if(MSVC) # TODO: non-msvc
    # Link with the multithreaded runtime for the selected configuration.
    set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")

    add_compile_options(
        /GF              # Enable string pooling.
        /Gy              # Enable function-level linking.
        /MP              # Enable multi-processor compilation.
        /permissive-     # Enable conformance mode.
        /Zc:preprocessor # Enable conformant preprocessor.
        /W4              # Warning level 4.
        /WX              # Treat warnings as errors.
        /wd4996          # Disable unsafe strcat and friends' warnings.
        /wd5054          # Disable "operator '|': deprecated between enumerations of different types".
        /wd5105          # Disable "macro expansion producing 'defined' has undefined behavior" from windows' code.
    )
    
    set_property(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR} PROPERTY VS_SOLUTION_ITEMS
        .gitattributes
        .gitignore
        .gitmodules
        CMakeLists.txt
        README.md
        LICENSE.md
    )
endif()

add_subdirectory(Dependencies/imgui)

add_library(implot)
file(GLOB PROJECT_SOURCES CONFIGURE_DEPENDS
    ${CMAKE_CURRENT_SOURCE_DIR}/*.h
    ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp)
target_sources(implot PRIVATE ${PROJECT_SOURCES})
target_link_libraries(implot imgui)
target_include_directories(implot PRIVATE
    ${CMAKE_SOURCE_DIR}/Dependencies/imgui/
)

if(NOT "${CMAKE_SOURCE_DIR}" STREQUAL "${CMAKE_CURRENT_SOURCE_DIR}")
    set_target_properties(implot PROPERTIES FOLDER Dependencies)
endif()
