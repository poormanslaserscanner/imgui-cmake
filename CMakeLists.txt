cmake_minimum_required(VERSION 2.8.12)
project(imgui)

include(GNUInstallDirs)

set(inst_src_dir ${CMAKE_INSTALL_FULL_INCLUDEDIR}/imgui_src)
set(inst_src_dir0 ${CMAKE_INSTALL_INCLUDEDIR}/imgui_src)

set(root ${CMAKE_CURRENT_SOURCE_DIR}/imgui)
set(phdrs ${root}/imgui.h)
set(hdrs ${root}/imstb_rectpack.h ${root}/imstb_textedit.h ${root}/imstb_truetype.h ${root}/imgui_internal.h ${root}/examples/imgui_impl_glfw.h ${root}/examples/imgui_impl_opengl3.h)

set(srcs              ${root}/imgui.cpp         ${root}/imgui_draw.cpp        ${root}/imgui_widgets.cpp         ${root}/imgui_demo.cpp ${root}/examples/imgui_impl_glfw.cpp ${root}/examples/imgui_impl_opengl3.cpp)
set(inst_srcs ${inst_src_dir}/imgui.cpp ${inst_src_dir}/imgui_draw.cpp ${inst_src_dir}/imgui_widgets.cpp ${inst_src_dir}/imgui_demo.cpp ${inst_src_dir}/imgui_impl_glfw.cpp  ${inst_src_dir}/imgui_impl_opengl3.cpp)

add_library(imgui INTERFACE)

if(CMAKE_VERSION VERSION_LESS 3.3)
    message(WARNING "The config-module created by your CMake version (< 3.3) "
        "can't attach the imgui source files to the exported library. Please see the "
        "documentation in `README.md` for further instructions.")
else()
    message(${inst_src_dir})
    message(${root})
    message("${srcs}")
    message("${inst_srcs}")

    target_sources(imgui INTERFACE
        $<BUILD_INTERFACE:${srcs}>
        $<INSTALL_INTERFACE:${inst_srcs}>)
endif()

set(IMGUI_SOURCES ${srcs})

install(FILES ${srcs} ${hdrs} DESTINATION ${inst_src_dir0})

target_include_directories(imgui INTERFACE
    $<BUILD_INTERFACE:${root}>
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>)

target_compile_definitions(imgui INTERFACE IMGUI_DISABLE_INCLUDE_IMCONFIG_H)

install(TARGETS imgui EXPORT imgui-targets DESTINATION ${CMAKE_INSTALL_LIBDIR})

install(EXPORT imgui-targets
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/imgui)

install(FILES ${phdrs} DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})
install(FILES ${root}/imconfig.h DESTINATION ${CMAKE_INSTALL_INCLUDEDIR} RENAME imconfig-sample.h)

# create config-module

set(cm ${CMAKE_CURRENT_BINARY_DIR}/hide/imgui-config.cmake)

file(WRITE ${cm} "include(\${CMAKE_CURRENT_LIST_DIR}/imgui-targets.cmake)\n")

if(CMAKE_VERSION VERSION_LESS 3.3)
    file(APPEND ${cm}
        "get_filename_component(IMGUI_SOURCES \"\${CMAKE_CURRENT_LIST_DIR}/../../../include/imgui_src/imgui.cpp\" ABSOLUTE)\n")
endif()

install(FILES ${cm} DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/imgui)
