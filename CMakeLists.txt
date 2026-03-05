cmake_minimum_required(VERSION 3.30)

set(CMAKE_TOOLCHAIN_FILE "${CMAKE_CURRENT_SOURCE_DIR}/vcpkg/scripts/buildsystems/vcpkg.cmake" CACHE STRING "")
project(ASPETIC VERSION 1.0)

set(CMAKE_CXX_STANDARD 20)

# 1. FIND DEPENDENCIES
find_package(glad CONFIG REQUIRED)
find_package(glfw3 CONFIG REQUIRED)
find_package(spdlog CONFIG REQUIRED)
find_package(fmt CONFIG REQUIRED)

# -------------------------------------------------------------------
# 2. THE ENGINE (ASPETIC) - This is a LIBRARY
# -------------------------------------------------------------------
file(GLOB_RECURSE ENGINE_SOURCES "src/Aspetic/*.cpp" "src/Aspetic/*.h")

add_library(Aspetic_Lib STATIC ${ENGINE_SOURCES})

target_include_directories(Aspetic_Lib PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}/src")

target_link_libraries(Aspetic_Lib PUBLIC 
    glad::glad glfw spdlog::spdlog fmt::fmt
)

# -------------------------------------------------------------------
# 3. THE GAME (SANDBOX) - This is the EXECUTABLE
# -------------------------------------------------------------------
add_executable(Sandbox "src/Sandbox/SandboxApp.cpp")

# THE MAGIC LINK: Sandbox "swallows" the Engine
target_link_libraries(Sandbox PRIVATE Aspetic_Lib)

# Mac Specifics for the final app
if(APPLE)
    target_link_libraries(Sandbox PRIVATE "-framework OpenGL" "-framework Cocoa" "-framework IOKit")
endif()