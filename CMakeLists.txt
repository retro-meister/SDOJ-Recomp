# saidaioujou_recomp_tu1 - ReXGlue Recompiled Project
#
# SDK boilerplate lives in generated/rexglue.cmake.

cmake_minimum_required(VERSION 3.25)
project(saidaioujou_recomp_tu1 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(REXSDK_DIR "${CMAKE_CURRENT_SOURCE_DIR}/thirdparty/rexglue-sdk"
    CACHE PATH "Path to the bundled ReXGlue SDK")

include(generated/rexglue.cmake)

# Sources
set(SAIDAIOUJOURECOMPTU1_SOURCES
    src/main.cpp
)

if(WIN32)
    add_executable(saidaioujou_recomp_tu1 WIN32 ${SAIDAIOUJOURECOMPTU1_SOURCES})
else()
    add_executable(saidaioujou_recomp_tu1 ${SAIDAIOUJOURECOMPTU1_SOURCES})
endif()

rexglue_setup_target(saidaioujou_recomp_tu1 GPU_PLUGINS xenos)

if(APPLE)
    # Required by rex_app.cpp in source-tree SDK builds.
    if(TARGET imgui::imgui)
        target_include_directories(saidaioujou_recomp_tu1 PRIVATE
            $<TARGET_PROPERTY:imgui::imgui,INTERFACE_INCLUDE_DIRECTORIES>)
    endif()
    if(TARGET rexcore)
        target_include_directories(saidaioujou_recomp_tu1 PRIVATE
            $<TARGET_PROPERTY:rexcore,INTERFACE_INCLUDE_DIRECTORIES>)
    endif()

    if(TARGET Tracy::TracyClient)
        add_custom_command(TARGET saidaioujou_recomp_tu1 POST_BUILD
            COMMAND ${CMAKE_COMMAND} -E copy_if_different
                $<TARGET_FILE:Tracy::TracyClient>
                $<TARGET_FILE_DIR:saidaioujou_recomp_tu1>
            VERBATIM)
    endif()

    add_custom_target(stage_macos_launcher
        COMMAND ${CMAKE_COMMAND} -E copy_if_different
            "${CMAKE_CURRENT_SOURCE_DIR}/launcher/launch.sh"
            "$<TARGET_FILE_DIR:saidaioujou_recomp_tu1>/launch.sh"
        COMMAND ${CMAKE_COMMAND} -E copy_if_different
            "${CMAKE_CURRENT_SOURCE_DIR}/launcher/prepare_game.sh"
            "$<TARGET_FILE_DIR:saidaioujou_recomp_tu1>/prepare_game.sh"
        COMMAND /bin/chmod +x
            "$<TARGET_FILE_DIR:saidaioujou_recomp_tu1>/launch.sh"
            "$<TARGET_FILE_DIR:saidaioujou_recomp_tu1>/prepare_game.sh"
        SOURCES
            "${CMAKE_CURRENT_SOURCE_DIR}/launcher/launch.sh"
            "${CMAKE_CURRENT_SOURCE_DIR}/launcher/prepare_game.sh"
        VERBATIM)
    add_dependencies(saidaioujou_recomp_tu1 stage_macos_launcher)
endif()
