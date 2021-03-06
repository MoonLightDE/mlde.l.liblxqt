cmake_minimum_required(VERSION 2.8.11)

project(liblxqt)

set(LXQT_MAJOR_VERSION 0)
set(LXQT_MINOR_VERSION 8)
set(LXQT_PATCH_VERSION 0)

include(CheckCXXCompilerFlag)
CHECK_CXX_COMPILER_FLAG("-std=c++11" COMPILER_SUPPORTS_CXX11)
CHECK_CXX_COMPILER_FLAG("-std=c++0x" COMPILER_SUPPORTS_CXX0X)
if(COMPILER_SUPPORTS_CXX11)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
elseif(COMPILER_SUPPORTS_CXX0X)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++0x")
else()
    message(FATAL "The compiler ${CMAKE_CXX_COMPILER} has no C++11 support. C++11 support is required")
endif()

# Set default installation paths
set(LIB_INSTALL_DIR "lib${LIB_SUFFIX}" CACHE PATH "Installation path for libraries")
set(LXQT_PKG_CONFIG_DESCRIPTION "Shared library for LXQt applications")

set(PUB_HDRS
    aboutdialog/lxqtaboutdialog.h
    addplugindialog/lxqtaddplugindialog.h
    lxqthtmldelegate.h
    lxqtsettings.h
    lxqtxfitman.h
    lxqtplugininfo.h
    lxqtpowermanager.h
    lxqtscreensaver.h
    lxqtapplication.h
    lxqttranslator.h
    lxqtprogramfinder.h

    configdialog/lxqtconfigdialog.h
    configdialog/lxqtpageselectwidget.h

    lxqtpower/lxqtpower.h
    lxqtnotification.h
    lxqtautostartentry.h
    lxqtgridlayout.h
    lxqtrotatedwidget.h
    lxqtglobals.h
)

set(PUBLIC_CLASSES
    AboutDialog
    HtmlDelegate
    Settings
    XfitMan
    PluginInfo
    PowerManager
    AddPluginDialog
    ScreenSaver
    Application
    Translator
    ProgramFinder

    ConfigDialog
    PageSelectWidget

    Power
    Notification
    AutostartEntry
    GridLayout
    RotatedWidget
)

set(PRIV_HDRS
    aboutdialog/lxqtaboutdialog_p.h
    aboutdialog/technicalinfo.h
    translatorsinfo/translatorsinfo.h
)

set(SRCS
    aboutdialog/lxqtaboutdialog.cpp
    aboutdialog/technicalinfo.cpp
    lxqtxfitman.cpp
    lxqthtmldelegate.cpp
    lxqtplugininfo.cpp
    lxqtpowermanager.cpp
    addplugindialog/lxqtaddplugindialog.cpp
    lxqtsettings.cpp
    lxqtscreensaver.cpp
    lxqtapplication.cpp
    lxqttranslator.cpp
    lxqtprogramfinder.cpp

    configdialog/lxqtconfigdialog.cpp
    configdialog/lxqtpageselectwidget.cpp

    lxqtpower/lxqtpower.cpp
    lxqtpower/lxqtpowerproviders.cpp
    lxqtautostartentry.cpp
    lxqtnotification.cpp
    lxqtgridlayout.cpp
    lxqtrotatedwidget.cpp
    translatorsinfo/translatorsinfo.cpp
)

set(MOCS
    aboutdialog/lxqtaboutdialog.h
    aboutdialog/lxqtaboutdialog_p.h
    addplugindialog/lxqtaddplugindialog.h
    lxqthtmldelegate.h
    lxqtpowermanager.h
    lxqtsettings.h
    lxqtscreensaver.h
    lxqtapplication.h

    configdialog/lxqtconfigdialog.h
    configdialog/lxqtpageselectwidget.h

    lxqtnotification.h
    lxqtnotification_p.h
    lxqtpower/lxqtpower.h
    lxqtpower/lxqtpowerproviders.h
    lxqtgridlayout.h
    lxqtrotatedwidget.h
)

set(FORMS
    aboutdialog/lxqtaboutdialog.ui
    configdialog/lxqtconfigdialog.ui
    addplugindialog/lxqtaddplugindialog.ui
)

# additional cmake files
list(APPEND CMAKE_MODULE_PATH
    ${CMAKE_CURRENT_SOURCE_DIR}/cmake
    ${CMAKE_CURRENT_SOURCE_DIR}/cmake/modules
)

option(UPDATE_TRANSLATIONS "Update source translation translations/*.ts files" OFF)

set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

set(CMAKE_AUTOMOC ON)
find_package(Qt5Widgets REQUIRED QUIET)
find_package(Qt5DBus REQUIRED QUIET)
find_package(Qt5X11Extras REQUIRED QUIET)
find_package(Qt5LinguistTools REQUIRED QUIET)
find_package(Qt5Xdg REQUIRED)
find_package(X11 REQUIRED)
message(STATUS "Building with Qt ${Qt5Core_VERSION_STRING}")

QT5_WRAP_UI(UIS ${FORMS})

QT5_ADD_DBUS_INTERFACE(SRCS
    dbus/org.freedesktop.Notifications.xml
    notifications_interface
)

set(LXQT_QT_VERSION "5")
set(LXQT_QT_VERSION_SUFFIX "-qt5")
set(LXQT_PKG_CONFIG_REQUIRES "Qt5Xdg Qt5Widgets Qt5Xml Qt5DBus Qt5X11Extras")

# Standard directories for installation
include(GNUInstallDirs)

#-----------------------------------------------------------------------------
# LXQT_DATA_DIR defaults to CMAKE_INSTALL_FULL_DATADIR. It's added to
#   XDG_DATA_DIRS by the startlxqt script
# Warning: Setting LXQT_DATA_DIR must be done after including GNUInstallDirs
#-----------------------------------------------------------------------------
if (NOT DEFINED LXQT_DATA_DIR)
    set(LXQT_DATA_DIR "${CMAKE_INSTALL_FULL_DATADIR}" CACHE PATH
        "LXQt base directory relative to which data files should be searched"
    )
endif()

set(LXQT_VERSION ${LXQT_MAJOR_VERSION}.${LXQT_MINOR_VERSION}.${LXQT_PATCH_VERSION})
set(LXQT_LIBRARY_NAME "lxqt${LXQT_QT_VERSION_SUFFIX}")
set(LXQT_RELATIVE_SHARE_DIR "${LXQT_LIBRARY_NAME}")
set(LXQT_SHARE_DIR "${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_DATAROOTDIR}/${LXQT_RELATIVE_SHARE_DIR}")
set(LXQT_RELATIVE_TRANSLATIONS_DIR "${LXQT_LIBRARY_NAME}/translations")
set(LXQT_TRANSLATIONS_DIR "${LXQT_SHARE_DIR}/translations")

set(LXQT_INTREE_INCLUDE_DIR "${CMAKE_BINARY_DIR}${CMAKE_FILES_DIRECTORY}/include")
set(LXQT_INTREE_TARGETS_FILE "${CMAKE_BINARY_DIR}/${LXQT_LIBRARY_NAME}-targets.cmake")

set(LXQT_INSTALL_INCLUDE_DIR "${CMAKE_INSTALL_FULL_INCLUDEDIR}/${LXQT_LIBRARY_NAME}")
set(LXQT_INSTALL_CMAKE_DIR "${CMAKE_INSTALL_PREFIX}/share/cmake")

include(cmake/FindInstallConfigPath.cmake)
include(create_portable_headers)

## Translations
include(LXQtTranslateTs)

lxqt_translate_ts(QM_FILES
    UPDATE_TRANSLATIONS
        ${UPDATE_TRANSLATIONS}
    SOURCES
        ${SRCS}
        ${FORMS}
    INSTALL_DIR
        ${LXQT_TRANSLATIONS_DIR}/${PROJECT_NAME}
)

message(STATUS "liblxqt version: ${LXQT_VERSION}")


# Copy public headers
foreach(h ${PUB_HDRS})
    get_filename_component(bh ${h} NAME)
    configure_file(${h} "${LXQT_INTREE_INCLUDE_DIR}/LXQt/${bh}" COPYONLY)
endforeach()

# Create the portable headers
create_portable_headers(INTREE_PORTABLE_HEADERS ${LXQT_INTREE_INCLUDE_DIR}/LXQt ${PUBLIC_CLASSES})
check_portable_headers(H_FILES ${PUB_HDRS} LINKS ${INTREE_PORTABLE_HEADERS})

#************************************************
# Create in-tree build infrastructure
#************************************************
set(CFG_LXQT_INCLUDE_DIR    ${LXQT_INTREE_INCLUDE_DIR})
set(CFG_LXQT_LIBRARY        ${LXQT_LIBRARY_NAME})
set(CFG_LXQT_USE_FILE       ${CMAKE_BINARY_DIR}/${LXQT_LIBRARY_NAME}_use.cmake)
set(CFG_LXQT_TARGETS_FILE   ${LXQT_INTREE_TARGETS_FILE})
set(CFG_LXQT_CMAKE_MODULES_DIR "${PROJECT_SOURCE_DIR}/cmake/modules")

configure_file(
    ${CMAKE_CURRENT_SOURCE_DIR}/cmake/lxqtX-config.cmake.in
    ${CMAKE_BINARY_DIR}/${LXQT_LIBRARY_NAME}-config.cmake
    @ONLY
)

configure_file(
    ${CMAKE_CURRENT_SOURCE_DIR}/cmake/lxqt_use.cmake.in
    ${CMAKE_BINARY_DIR}/${LXQT_LIBRARY_NAME}_use.cmake
    @ONLY
)

include(${CFG_LXQT_USE_FILE})


#************************************************
# Create installable build infrastructure
#************************************************
set(CFG_LXQT_INCLUDE_DIR "${LXQT_INSTALL_INCLUDE_DIR}")
set(CFG_LXQT_LIBRARY ${LXQT_LIBRARY_NAME})
set(CFG_LXQT_USE_FILE "${LXQT_INSTALL_CMAKE_DIR}/${LXQT_LIBRARY_NAME}/${LXQT_LIBRARY_NAME}_use.cmake")
set(CFG_LXQT_TARGETS_FILE "${LXQT_INSTALL_CMAKE_DIR}/${LXQT_LIBRARY_NAME}/${LXQT_LIBRARY_NAME}-targets.cmake")
set(CFG_LXQT_CMAKE_MODULES_DIR "${LXQT_INSTALL_CMAKE_DIR}/${LXQT_LIBRARY_NAME}/modules")

configure_file(
    "${CMAKE_CURRENT_SOURCE_DIR}/cmake/lxqtX-config.cmake.in"
    "${CMAKE_CURRENT_BINARY_DIR}/install/${LXQT_LIBRARY_NAME}-config.cmake"
    @ONLY
)

# use gcc visibility feature to decrease unnecessary exported symbols
if (CMAKE_COMPILER_IS_GNUCXX)
    # set visibility to hidden to hide symbols, unlesss they're exported
    # manually in the code
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fvisibility=hidden -fvisibility-inlines-hidden -fno-exceptions -Wl,-no-undefined -Wall")
endif()
add_definitions(-DCOMPILE_LIBLXQT)

string (TOLOWER "${CMAKE_BUILD_TYPE}" CMAKE_BUILD_TYPE)
if (CMAKE_BUILD_TYPE STREQUAL "release")
    add_definitions(-DQT_NO_DEBUG_OUTPUT -DQT_NO_WARNING_OUTPUT)
endif()

configure_file(
    ${CMAKE_CURRENT_SOURCE_DIR}/cmake/lxqt_use.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/install/${LXQT_LIBRARY_NAME}_use.cmake
    @ONLY
)

install(FILES
    ${CMAKE_CURRENT_BINARY_DIR}/install/${LXQT_LIBRARY_NAME}-config.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/install/${LXQT_LIBRARY_NAME}_use.cmake
    DESTINATION "${CMAKE_INSTALL_DATADIR}/cmake/${LXQT_LIBRARY_NAME}"
    COMPONENT Devel
)

install(EXPORT
    ${LXQT_LIBRARY_NAME}-targets
    DESTINATION "${CMAKE_INSTALL_DATADIR}/cmake/${LXQT_LIBRARY_NAME}"
    COMPONENT Devel
)

file(GLOB toInstallModules
    "${PROJECT_SOURCE_DIR}/cmake/modules/*.cmake"
    "${PROJECT_SOURCE_DIR}/cmake/modules/*.in"
)

install(
    FILES ${toInstallModules}
    DESTINATION  "${CMAKE_INSTALL_DATADIR}/cmake/${LXQT_LIBRARY_NAME}/modules"
    COMPONENT Devel
)


include(translatorsinfo/CMakeLists.txt)
get_translatorsinfo_qrc(translatorsinfo_qrc)
qt5_add_resources(QRC_CXX_SOURCES ${translatorsinfo_qrc})

add_library(${LXQT_LIBRARY_NAME}
    SHARED ${PUB_HDRS}
           ${PRIV_HDRS}
           ${SRCS}
           ${dbus_generated}
           ${UIS} ${MOCS}
           ${QM_FILES}
           ${QRC_CXX_SOURCES}

)

target_link_libraries(${LXQT_LIBRARY_NAME}
    PUBLIC
    Qt5::Widgets
    Qt5::DBus
    Qt5::X11Extras
    Qt5Xdg
    X11
)

set_target_properties(${LXQT_LIBRARY_NAME} PROPERTIES
    VERSION   ${LXQT_VERSION}
    SOVERSION ${LXQT_MAJOR_VERSION}
)

install(TARGETS
    ${LXQT_LIBRARY_NAME}
    DESTINATION ${CMAKE_INSTALL_LIBDIR}
    EXPORT ${LXQT_LIBRARY_NAME}-targets
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
)


export(TARGETS ${LXQT_LIBRARY_NAME} ${QTXDG_TARGET} FILE ${LXQT_INTREE_TARGETS_FILE})

install(FILES ${PUB_HDRS} DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}/LXQt")
install(FILES ${INTREE_PORTABLE_HEADERS} DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}/LXQt")

#************************************************
# Create pkgconfig file
#************************************************
lxqt_create_pkgconfig_file(${LXQT_LIBRARY_NAME}
    ${LXQT_PKG_CONFIG_DESCRIPTION}
    ${LXQT_PKG_CONFIG_REQUIRES}
    ${LXQT_LIBRARY_NAME}
    ${LXQT_VERSION}
)
#************************************************


# building tarball with CPack -------------------------------------------------
include(InstallRequiredSystemLibraries)
set(CPACK_PACKAGE_VERSION_MAJOR ${LXQT_MAJOR_VERSION})
set(CPACK_PACKAGE_VERSION_MINOR ${LXQT_MINOR_VERSION})
set(CPACK_PACKAGE_VERSION_PATCH ${LXQT_PATCH_VERSION})
set(CPACK_GENERATOR TBZ2)
set(CPACK_SOURCE_GENERATOR TBZ2)
set(CPACK_SOURCE_IGNORE_FILES /build/;.gitignore;.*~;.git;.kdev4;temp)
include(CPack)
