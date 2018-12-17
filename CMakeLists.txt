# (c) 2008 by BogDan Vatra < bogdan@licentia.eu >

cmake_minimum_required(VERSION 2.6)
include("cmake/HunterGate.cmake")
HunterGate(
    URL "https://github.com/ruslo/hunter/archive/v0.23.44.tar.gz"
    SHA1 "c4cfcc0cd39fdae8de08f6205b7f34cab4a7ba79"
)
project(QtPropertyEditor VERSION 2.1.3)

# please choose between GPL or LGPL license
set (PROPERTYEDITOR_LICENSE "LGPL")

message("*** propertyEditor use ${PROPERTYEDITOR_LICENSE} license ***")

if(BUILD_SHARED_LIBS)
    set (LIB_TYPE SHARED)
else(BUILD_SHARED_LIBS)
    set (LIB_TYPE STATIC)
endif()

set(CMAKE_INCLUDE_CURRENT_DIR ON)

#set(CMAKE_VERBOSE_MAKEFILE ON)
#comment or remove the above line before release

set (PROPERTYEDITOR_VERSION_MAJOR 2)
set (PROPERTYEDITOR_VERSION_MINOR 1)
set (PROPERTYEDITOR_VERSION_RELEASE 3)
set (PROPERTYEDITOR_VERSION "${PROPERTYEDITOR_VERSION_MAJOR}.${PROPERTYEDITOR_VERSION_MINOR}.${PROPERTYEDITOR_VERSION_RELEASE}" )

add_definitions (-DPROPERTYEDITOR_VERSION=\"${PROPERTYEDITOR_VERSION}\")

include(GNUInstallDirs)

set(generated_dir "${CMAKE_CURRENT_BINARY_DIR}/generated")
set(version_config "${generated_dir}/${PROJECT_NAME}ConfigVersion.cmake")
set(project_config "${generated_dir}/${PROJECT_NAME}Config.cmake")
set(config_install_dir "${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME}")
set(INCLUDE_INSTALL_DIR "include")
set(targets_export_name "${PROJECT_NAME}Targets")
set(namespace "${PROJECT_NAME}::")

include(CMakePackageConfigHelpers)

write_basic_package_version_file(
    "${version_config}" COMPATIBILITY SameMajorVersion
)

configure_package_config_file(
    "cmake/config.cmake.in"
    "${project_config}"
    INSTALL_DESTINATION "${config_install_dir}"
)

hunter_add_package(Qt COMPONENTS qtbase)

find_package(Qt5Core CONFIG REQUIRED)
find_package(Qt5Widgets CONFIG REQUIRED)

set(CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake/modules" )
#include(SetPaths.cmake)

set(INSTALL_TARGETS_DEFAULT_ARGS  EXPORT "${targets_export_name}"
                                  RUNTIME DESTINATION bin
                                  LIBRARY DESTINATION lib
                                  ARCHIVE DESTINATION lib COMPONENT Devel 
                                  INCLUDES DESTINATION "${INCLUDE_INSTALL_DIR}")


set(INSTALL_PLUGINS_DEFAULT_ARGS  RUNTIME DESTINATION "bin/propertyEditor"
                                  LIBRARY DESTINATION "lib/propertyEditor"
                                  ARCHIVE DESTINATION "lib/propertyEditor" COMPONENT Devel )


IF(APPLE)
    IF(NOT PROPERTYEDITOR_HAS_BEEN_RUN_BEFORE)
        IF(EXISTS /Developer/SDKs/MacOSX10.5.sdk OR EXISTS /SDKs/MacOSX10.5.sdk)
            SET(CMAKE_OSX_ARCHITECTURES "ppc;i386;ppc64;x86_64" CACHE STRING "Build architectures for OSX" FORCE)
            SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fvisibility-inlines-hidden" CACHE STRING "Flags used by the compiler during all build types." FORCE)
        ELSE(EXISTS /Developer/SDKs/MacOSX10.5.sdk OR EXISTS /SDKs/MacOSX10.5.sdk)
            IF(EXISTS /Developer/SDKs/MacOSX10.4u.sdk OR EXISTS /SDKs/MacOSX10.4u.sdk)
                SET(CMAKE_OSX_ARCHITECTURES "ppc;i386" CACHE STRING "Build architectures for OSX" FORCE)
                SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fvisibility-inlines-hidden" CACHE STRING "Flags used by the compiler during all build types." FORCE)
           ENDIF(EXISTS /Developer/SDKs/MacOSX10.4u.sdk OR EXISTS /SDKs/MacOSX10.4u.sdk)
        ENDIF(EXISTS /Developer/SDKs/MacOSX10.5.sdk OR EXISTS /SDKs/MacOSX10.5.sdk)
        message("Build architectures for OSX:${CMAKE_OSX_ARCHITECTURES}")
    ENDIF(NOT PROPERTYEDITOR_HAS_BEEN_RUN_BEFORE)
ENDIF(APPLE)

set (CMAKE_PREFIX_PATH)

set( QT_USE_QTGUI   TRUE )

include_directories(
        ${CMAKE_CURRENT_SOURCE_DIR}
        ${Qt5Widgets_INCLUDE_DIRS} ${Qt5Core_INCLUDE_DIRS}
        ${CMAKE_CURRENT_BINARY_DIR})

#add_definitions (-DLIB_INSTALL_DIR=\"${LIB_INSTALL_DIR}\")

add_subdirectory(lib)
add_subdirectory(plugins)

CONFIGURE_FILE(
  "${CMAKE_CURRENT_SOURCE_DIR}/cmake_uninstall.cmake.in"
  "${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake"
  IMMEDIATE @ONLY)

ADD_CUSTOM_TARGET(uninstall
  "${CMAKE_COMMAND}" -P "${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake")

# This needs to be run very last so other parts of the scripts can take
# advantage of this.
IF(NOT PROPERTYEDITOR_HAS_BEEN_RUN_BEFORE)
    SET(PROPERTYEDITOR_HAS_BEEN_RUN_BEFORE 1 CACHE INTERNAL "Flag to track whether this is the first time running CMake or if CMake has been configured before")
ENDIF(NOT PROPERTYEDITOR_HAS_BEEN_RUN_BEFORE)

install(
    FILES "${project_config}" "${version_config}"
    DESTINATION "${config_install_dir}"
)

install(
    EXPORT "${targets_export_name}"
    NAMESPACE "${namespace}"
    DESTINATION "${config_install_dir}"
)
