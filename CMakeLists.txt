cmake_minimum_required(VERSION 3.30)

# 1. THE TOOLCHAIN (Must stay at the top)
set(CMAKE_TOOLCHAIN_FILE "${CMAKE_CURRENT_SOURCE_DIR}/vcpkg/scripts/buildsystems/vcpkg.cmake"
    CACHE STRING "Vcpkg toolchain file")

project(ASPETIC VERSION 1.0)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED True)

# 2. FIND DEPENDENCIES (Vcpkg needs to be told what to find)
find_package(glad CONFIG REQUIRED)
find_package(glfw3 CONFIG REQUIRED)
find_package(spdlog CONFIG REQUIRED)
find_package(imgui CONFIG REQUIRED)
find_package(glm CONFIG REQUIRED)

# 3. GATHER SOURCES
# Note: I changed 'Playground_SOURCES' to 'ASPETIC_SOURCES' to match your target_sources below
file(GLOB_RECURSE ASPETIC_SOURCES CONFIGURE_DEPENDS 
    "${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp"
    "${CMAKE_CURRENT_SOURCE_DIR}/src/*.h"
    "${CMAKE_CURRENT_SOURCE_DIR}/src/*.hpp"
    "${CMAKE_CURRENT_SOURCE_DIR}/src/*.inl"
)

# 4. CREATE EXECUTABLE
add_executable(ASPETIC)
target_sources(ASPETIC PRIVATE ${ASPETIC_SOURCES})

# 5. INCLUDE DIRS
target_include_directories(ASPETIC PRIVATE "${CMAKE_CURRENT_SOURCE_DIR}/src")

# 6. LINK LIBRARIES (The "Cherno" Essentials)
target_link_libraries(ASPETIC PRIVATE
    glad::glad
    glfw
    spdlog::spdlog
    imgui::imgui
    glm::glm
)

# 7. MAC FRAMEWORKS (Crucial for macOS Windowing)
if(APPLE)
    target_link_libraries(ASPETIC PRIVATE 
        "-framework OpenGL"
        "-framework Cocoa"
        "-framework IOKit"
        "-framework CoreVideo"
    )
endif()

# 8. COMPILER OPTIONS
if(MSVC)
    target_compile_options(ASPETIC PRIVATE /W3 /MP /WX /ZI)
else()
    # Mac/Linux options
    target_compile_options(ASPETIC PRIVATE -Wall -Wextra -Wno-unused-parameter)
endif()

# 9. IDE ORGANIZATION (Visual Studio/Xcode Folders)
source_group(TREE "${CMAKE_CURRENT_SOURCE_DIR}/src" PREFIX "Source Files" FILES ${ASPETIC_SOURCES})
set_property(GLOBAL PROPERTY USE_FOLDERS ON)