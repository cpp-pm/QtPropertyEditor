# (c) 2008 by BogDan Vatra < bogdan@licentia.eu >

project(FontProperty)

include_directories(BEFORE "${CMAKE_SOURCE_DIR}/lib" )

set(Font_SRCS changefont.cpp  font.cpp)
QT5_WRAP_CPP(Font_SRCS changefont.h  font.h)

add_library(FontProperty ${LIB_TYPE} ${Font_SRCS})
add_dependencies(FontProperty PropertyEditor)
link_directories( "${CMAKE_BINARY_DIR}/lib" )
target_link_libraries(FontProperty PropertyEditor ${Qt5Widgets_LIBRARIES}
        ${Qt5Core_LIBRARIES} )

install(TARGETS FontProperty ${INSTALL_PLUGINS_DEFAULT_ARGS})
