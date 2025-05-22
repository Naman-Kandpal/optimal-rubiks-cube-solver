cmake_minimum_required(VERSION 3.10) # Increased minimum version for modern CMake features

project(RubiksCubeSolver CXX) # Specify CXX for C++ project


# '2a' is for C++20. Ensure your g++ 13+ supports this.

set(CMAKE_CXX_STANDARD 20) 
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF) 


set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall")

set(SRC "${CMAKE_SOURCE_DIR}/main.cpp"
        "${CMAKE_SOURCE_DIR}/Cube/cube.cpp"
        "${CMAKE_SOURCE_DIR}/Engine/engine.cpp"
        "${CMAKE_SOURCE_DIR}/Util/databasegenerator.cpp"
        "${CMAKE_SOURCE_DIR}/Util/searchutil.cpp"
        "${CMAKE_SOURCE_DIR}/Solver/database.cpp"
        "${CMAKE_SOURCE_DIR}/Solver/searcher.cpp"
        "${CMAKE_SOURCE_DIR}/Solver/Thistlethwaite/thistlethwaite.cpp"
        "${CMAKE_SOURCE_DIR}/Solver/Thistlethwaite/Databases/G0_G1_database.cpp"
        "${CMAKE_SOURCE_DIR}/Solver/Thistlethwaite/Databases/G1_G2_database.cpp"
        "${CMAKE_SOURCE_DIR}/Solver/Thistlethwaite/Databases/G2_G3_database.cpp"
        "${CMAKE_SOURCE_DIR}/Solver/Thistlethwaite/Databases/G3_G4_database.cpp"
        "${CMAKE_SOURCE_DIR}/Solver/Thistlethwaite/Goals/G0_G1_goal.cpp"
        "${CMAKE_SOURCE_DIR}/Solver/Thistlethwaite/Goals/G1_G2_goal.cpp"
        "${CMAKE_SOURCE_DIR}/Solver/Thistlethwaite/Goals/G2_G3_goal.cpp"
        "${CMAKE_SOURCE_DIR}/Solver/Thistlethwaite/Goals/G3_G4_goal.cpp")

add_executable(RubiksCubeSolver ${SRC})

if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE "Release")
endif()

# SDL2 integration (unified for all platforms when using modern CMake find_package)

# Find SDL2 and its components
# The original CMakeLists.txt only used core SDL2. If your project uses extensions,
# add them here (e.g., COMPONENTS Image Mixer TTF Gfx).
find_package(SDL2 REQUIRED) # Assuming only core SDL2 for this project

# Link against SDL2 libraries using imported targets
# SDL2::SDL2main is essential for Windows applications with SDL2
target_link_libraries(RubiksCubeSolver PRIVATE
    SDL2::SDL2main
    SDL2::SDL2
)

# Windows specific settings: Copy SDL2 DLLs to the executable directory
if(WIN32)
   
    set_target_properties(RubiksCubeSolver PROPERTIES VS_DEBUGGER_WORKING_DIRECTORY "${CMAKE_SOURCE_DIR}")

    # Copy SDL2.dll to the .exe file's directory
    # This uses the location of the SDL2::SDL2 imported target to find the DLL,
    # making it robust regardless of where SDL2 is installed by MSYS2.
    add_custom_command(TARGET RubiksCubeSolver POST_BUILD
        COMMAND ${CMAKE_COMMAND} -E copy_if_different
            $<TARGET_FILE_DIR:SDL2::SDL2>/../bin/SDL2.dll # Path relative to the library
            $<TARGET_FILE_DIR:RubiksCubeSolver>
        COMMAND_EXPAND_LISTS
    )

endif()