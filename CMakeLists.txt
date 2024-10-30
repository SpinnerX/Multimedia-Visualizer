cmake_minimum_required (VERSION 3.15)

# ffmpeg article to learn ffmpeg, http://unick-soft.ru/article.php?id=14
# ffmpeg article 2 to learn ffmpeg, https://friendlyuser.github.io/posts/tech/cpp/Using_FFmpeg_in_C++_A_Comprehensive_Guide

set(PROJECT_NAME VideoPlayer)

set(CMAKE_CXX_STANDARD 23)
add_compile_options(-fsanitize=address)
add_link_options(-fsanitize=address)

# set(CMAKE_EXPORT_COMPILE_COMMANDS ON CACHE INTERNAL "") # works (in creating the compile_commands.json file)
project(${PROJECT_NAME})

find_package(SDL2 REQUIRED)

# Finding opengl, glfw, glew, glut, and possibly freetype and even freeglut
find_package(glfw3 3.4 REQUIRED)
find_package(OpenGL REQUIRED)
find_package(GLEW REQUIRED)
find_package(GLUT REQUIRED)
find_package(FREEGLUT REQUIRED)

# Checking if glfw3 has been found meaning installed
if(NOT TARGET glfw)
    # message("GLFW HAS BEEN FOUND - NOT THE CMAKE MESSAGE BY YOUR MESSAGE!\n\n\n")
    message("glfw.cmake ERROR MESSAGE - glfw3 has not been found!")
    message("glfw.cmake ERROR MESSAGE - you should check if glfw has been installed!\n")
endif()


# Checking if OPENGL/GLEW/GLUT is found meaning installed
if(NOT OPENGL_FOUND)
    message("${Red}NOT CMAKE ACTUAL MESSAGE --> OPENGL NOT FOUND!\n\n")
endif()

if(NOT GLEW_FOUND)
    message("${Red}NOT CMAKE ACTUAL MESSAGE --> GLEW NOT FOUND!\n\n")
endif()

if(NOT GLUT_FOUND)
    message("${Red}NOT CMAKE ACTUAL MESSAGE --> GLUT NOT FOUND!\n\n")
endif()

if(APPLE)

# This is making sure we find the SDL libraries on mac
include_directories(${SDL2_INCLUDE_DIRS})

find_package(PkgConfig REQUIRED)
    pkg_check_modules(LIBAV REQUIRED IMPORTED_TARGET
    libavdevice
    libavfilter
    libavformat
    libavcodec
    libswresample
    libswscale
    libavutil
)

find_package(PkgConfig REQUIRED)
pkg_check_modules(gtk3 REQUIRED IMPORTED_TARGET gtk+-3.0)
pkg_search_module(gstreamer REQUIRED IMPORTED_TARGET gstreamer-1.0>=1.4)
pkg_search_module(gstreamer-sdp REQUIRED IMPORTED_TARGET gstreamer-sdp-1.0>=1.4)
pkg_search_module(gstreamer-app REQUIRED IMPORTED_TARGET gstreamer-app-1.0>=1.4)
pkg_search_module(gstreamer-video REQUIRED IMPORTED_TARGET gstreamer-video-1.0>=1.4)
endif(APPLE)



if(__APPLE__)

    # This is making sure we find the SDL libraries on mac
    include_directories(${SDL2_INCLUDE_DIRS})


    target_link_libraries(${PROJECT_NAME} pthread)
    # Linking the libraries for Cocoa, OpenGL, IOKit, and glfw
    # Need this for opengl
    target_link_libraries(${PROJECT_NAME} "-framework Cocoa")
    target_link_libraries(${PROJECT_NAME} "-framework OpenGL")
    target_link_libraries(${PROJECT_NAME} "-framework IOKit")
endif(__APPLE__)


# Create the executable
add_executable(${PROJECT_NAME} main.cpp videoreader.cpp)

target_link_libraries(
    ${PROJECT_NAME}
    glfw
    ${OPENGL_gl_LIBRARY}
    GLEW::GLEW
    ${GLUT_LIBRARIES} # NOTE: If you want to use freeglut, then you need this to link freeglut to get working
	${SDL2_LIBRARIES}
	PkgConfig::LIBAV
    PkgConfig::gtk3
    PkgConfig::gstreamer
    PkgConfig::gstreamer-sdp
    PkgConfig::gstreamer-app
    PkgConfig::gstreamer-video
)
