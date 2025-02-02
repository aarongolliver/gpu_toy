cmake_minimum_required(VERSION 3.9x)

project(GpuToy)

set(BUILD_SHARED_LIBS false)
set(CMAKE_CXX_STANDARD 17)

add_subdirectory(deps/SFML-2.5.1)
add_subdirectory(deps/simplefilewatcher)

add_library(gpu_toy_lib
    src/lib/file_watcher.cpp
    src/lib/gpu_toy.cpp
    src/lib/live_reloading_shader.cpp
    src/lib/clear_terminal.cpp
    src/lib/scope.cpp

    src/public/file_watcher.h
    src/public/filesystem_include.h
    src/public/gpu_toy.h
    src/public/live_reloading_shader.h
    src/public/scope.h
    src/public/shader_header.h
    src/public/clear_terminal.h
)

target_link_libraries(gpu_toy_lib PUBLIC
    sfml-graphics
    sfml-system
    sfml-window
    simplefilewatcher
)

if (UNIX)
    target_link_libraries(gpu_toy_lib PRIVATE stdc++fs)
endif()

add_executable(gpu_toy
    src/main/main.cpp
)

target_link_libraries(gpu_toy PRIVATE gpu_toy_lib)

target_include_directories(gpu_toy_lib PUBLIC src/public)

set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)

add_subdirectory(deps/googletest-1.8.1 EXCLUDE_FROM_ALL)

add_executable(gpu_toy_test
    src/test/file_watcher_test.cpp
    src/test/scope_test.cpp
)
target_link_libraries(gpu_toy_test gtest_main gpu_toy_lib)
enable_testing()
add_test(NAME    gpu_toy_test
         COMMAND gpu_toy_test)
