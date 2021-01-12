set(QT_MINIMUM_VERSION "5.9.0")

find_package(Qt6 COMPONENTS Core Widgets Gui REQUIRED)
#find_package(Qt5Core "${QT_MINIMUM_VERSION}" REQUIRED)
#find_package (Qt5Widgets REQUIRED)
#find_package (Qt5Gui REQUIRED)
#find_package (Qt5LinguistTools QUIET)

set(CMAKE_AUTOMOC ON)
#set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_INCLUDE_CURRENT_DIR ON)

include_directories(${CMAKE_SOURCE_DIR} ${CMAKE_CURRENT_BINARY_DIR}
                    ${Qt6Core_INCLUDE_DIRS} ${Qt6Widgets_INCLUDE_DIRS}
                    ${Qt6Gui_INCLUDE_DIRS})

add_definitions(-DDATADIR="${CMAKE_INSTALL_PREFIX}/share")

set(KP_SOURCES main.cpp)
set(KP_UI KvantumPreviewBase.ui)
set(KP_RESOURCES KvantumPreviewResources.qrc)

qt5_wrap_ui(KP_UIS_H ${KP_UI})
if (CMAKE_VERSION VERSION_LESS "3.10.0") # CMP0071 in 3.10: Let AUTOMOC and AUTOUIC process GENERATED files
  qt5_wrap_cpp(KP_HEADER KvantumPreview.h) # needed by "main.cpp"
else()
  set(KP_HEADER KvantumPreview.h)
endif()

# Translations
if(Qt5LinguistTools_FOUND)
  file(GLOB TS_FILES data/translations/kvantumpreview_*.ts)
  qt5_add_translation(QM_FILES ${TS_FILES})
  add_executable(kvantumpreview ${KP_SOURCES} ${KP_HEADER} ${KP_RESOURCES} ${KP_UI} ${DESKTOP_FILE} ${QM_FILES})
  install(FILES ${QM_FILES} DESTINATION "${CMAKE_INSTALL_PREFIX}/share/kvantumpreview/translations")
else(Qt5LinguistTools_FOUND)
  add_executable(kvantumpreview ${KP_SOURCES} ${KP_HEADER} ${KP_RESOURCES} ${KP_UI} ${DESKTOP_FILE})
endif(Qt5LinguistTools_FOUND)

target_link_libraries(kvantumpreview Qt6::Widgets Qt6::Core Qt6::Gui)

install(TARGETS kvantumpreview RUNTIME DESTINATION ${CMAKE_INSTALL_PREFIX}/bin)
