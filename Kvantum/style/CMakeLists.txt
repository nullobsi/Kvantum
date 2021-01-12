#set(QT_MINIMUM_VERSION "5.9.0")

if(NOT ENABLE_QT4)
  find_package(Qt6 COMPONENTS Core Svg Widgets Gui REQUIRED)
#  find_package(Qt6Core "${QT_MINIMUM_VERSION}" REQUIRED)
#  find_package(Qt6Widgets REQUIRED)
#  find_package(Qt6Gui REQUIRED)
#  find_package(Qt6Svg REQUIRED)
#  find_package(Qt6X11Extras REQUIRED)
  #if((Qt5Widgets_VERSION GREATER 5.11.0) OR (Qt5Widgets_VERSION EQUAL 5.11.0))
    find_package(KF5WindowSystem REQUIRED)
  #endif()
else()
  find_package(Qt4 4.8.7 REQUIRED)

  include(${QT_USE_FILE})
  add_definitions(${QT_DEFINITIONS})
endif()
find_package(X11)

set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_INCLUDE_CURRENT_DIR ON)

#if (NOT QT_CMAKE_PREFIX_PATH)
#set(QT_CMAKE_PREFIX_PATH $ENV{QT_CMAKE_PREFIX_PATH})
#endif ()
#set(CMAKE_PREFIX_PATH ${CMAKE_PREFIX_PATH} ${QT_CMAKE_PREFIX_PATH})
#set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_CURRENT_SOURCE_DIR}/cmake/modules/")

if(NOT ENABLE_QT4)
  execute_process(COMMAND "qmake-qt6" -query QT_INSTALL_PLUGINS
                  OUTPUT_VARIABLE _Qt6_PLUGIN_INSTALL_DIR
                  ERROR_QUIET OUTPUT_STRIP_TRAILING_WHITESPACE)
  set(KVANTUM_STYLE_DIR "${_Qt6_PLUGIN_INSTALL_DIR}/styles/")

  include_directories(${CMAKE_SOURCE_DIR} ${CMAKE_CURRENT_BINARY_DIR}
                      ${Qt6Core_INCLUDE_DIRS} ${Qt6Widgets_INCLUDE_DIRS}
                      ${Qt6Gui_INCLUDE_DIRS} ${Qt6Svg_INCLUDE_DIRS}
                      ${X11_INCLUDE_DIR})
else()
  set(KVANTUM_STYLE_DIR "${QT_PLUGINS_DIR}/styles/")

  include_directories(${CMAKE_SOURCE_DIR} ${CMAKE_CURRENT_BINARY_DIR}
                      ${QT_INCLUDE_DIR} ${QT_QTSVG_INCLUDE_DIR}
                      ${X11_INCLUDE_DIR})
endif()

add_definitions(-DDATADIR="${CMAKE_INSTALL_PREFIX}/share")

if(NOT ENABLE_QT4)
  if((Qt6Widgets_VERSION GREATER 5.15.0) OR (Qt6Widgets_VERSION EQUAL 5.15.0))
    set(kvantum_SOURCES
        Kvantum.cpp
        eventFiltering.cpp
        polishing.cpp
        rendering.cpp
        standardIcons.cpp
        KvantumPlugin.cpp
        shortcuthandler.cpp
        blur/blurhelper.cpp
        drag/windowmanager.cpp
        themeconfig/ThemeConfig.cpp
        animation/animation.cpp)
  else()
    set(kvantum_SOURCES
        Kvantum.cpp
        eventFiltering.cpp
        polishing.cpp
        rendering.cpp
        standardIcons.cpp
        KvantumPlugin.cpp
        shortcuthandler.cpp
        blur/blurhelper.cpp
        drag/windowmanager-old.cpp
        drag/x11wmmove.cpp
        themeconfig/ThemeConfig.cpp
        animation/animation.cpp)
  endif()
else()
  set(kvantum_SOURCES
      qt4/Kvantum4.cpp
      qt4/KvantumPlugin4.cpp
      shortcuthandler.cpp
      qt4/blurhelper4.cpp
      qt4/windowmanager4.cpp
      qt4/x11wmmove4.cpp
      themeconfig/ThemeConfig.cpp)
endif()

set(kvantum_RESOURCES themeconfig/defaulttheme.qrc)

add_library(kvantum SHARED ${kvantum_SOURCES} ${kvantum_RESOURCES})

if(NOT ENABLE_QT4)
  set(KDE_COLOR_DIR "${CMAKE_INSTALL_PREFIX}/share/color-schemes/")
else()
  set(KDE_COLOR_DIR "${CMAKE_INSTALL_PREFIX}/share/kde4/apps/color-schemes/")
endif()

if(NOT ENABLE_QT4)
  if((Qt6Widgets_VERSION GREATER 5.11.0) OR (Qt6Widgets_VERSION EQUAL 5.11.0))
    target_link_libraries(kvantum ${Qt6Core_LIBRARIES}
                                  ${Qt6Gui_LIBRARIES}
                                  ${Qt6Widgets_LIBRARIES}
                                  ${Qt6Svg_LIBRARIES}
                                  ${X11_LIBRARIES}
                                  KF5::WindowSystem)
  else()
    target_link_libraries(kvantum ${Qt6Core_LIBRARIES}
                                  ${Qt6Gui_LIBRARIES}
                                  ${Qt6Widgets_LIBRARIES}
                                  ${Qt6Svg_LIBRARIES}
                                  ${X11_LIBRARIES})
  endif()
else()
  target_link_libraries(kvantum ${QT_QTCORE_LIBRARY}
                                ${QT_QTGUI_LIBRARY}
                                ${QT_QTSVG_LIBRARY}
                                ${X11_LIBRARIES})
endif()

install(TARGETS kvantum LIBRARY DESTINATION ${KVANTUM_STYLE_DIR})
install(FILES "${CMAKE_SOURCE_DIR}/color/Kvantum.colors" DESTINATION ${KDE_COLOR_DIR})
