cmake_minimum_required(VERSION 3.5)

project(libthinkpad)

set(CMAKE_CXX_STANDARD 11)
add_definitions(-pthread)
add_definitions(-Wall)

set(SYSTEMD on)

set(SOURCES
    src/libthinkpad.cpp
    src/libthinkpad.h
)

add_library(thinkpad SHARED ${SOURCES})
set_property(TARGET thinkpad PROPERTY VERSION "2.6")
set_property(TARGET thinkpad PROPERTY SOVERSION 1)

configure_file(src/config.h.in config.h)

set(THINKPAD_LINK udev m)

if(DEFINED SYSTEMD)
    set(THINKPAD_LINK ${THINKPAD_LINK} systemd)
endif(DEFINED SYSTEMD)

find_package(Threads)

# Library
IF (DEFINED LIB)
  SET (LIB_INSTALL_DIR "${CMAKE_INSTALL_PREFIX}/${LIB}")
ELSE (DEFINED  LIB)
  IF (CMAKE_SIZEOF_VOID_P MATCHES "8")
    SET (LIB_SUFFIX "")
  ENDIF (CMAKE_SIZEOF_VOID_P MATCHES "8")
  SET (LIB_INSTALL_DIR "${CMAKE_INSTALL_PREFIX}/lib${LIB_SUFFIX}")
ENDIF (DEFINED  LIB)
MESSAGE (STATUS "Libraries will be installed in ${LIB_INSTALL_DIR}")

target_include_directories (thinkpad PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
target_include_directories (thinkpad PUBLIC ${CMAKE_CURRENT_BINARY_DIR})
target_link_libraries(thinkpad ${THINKPAD_LINK} ${CMAKE_THREAD_LIBS_INIT})

set_target_properties(thinkpad PROPERTIES PUBLIC_HEADER "src/libthinkpad.h")

install(TARGETS thinkpad
        LIBRARY DESTINATION ${LIB_INSTALL_DIR}
        PUBLIC_HEADER DESTINATION include
)

set(CPACK_PACKAGE_VENDOR "Ognjen Galic")
set(CPACK_PACKAGE_VERSION_MAJOR 2)
set(CPACK_PACKAGE_VERSION_MINOR 6)
set(CPACK_SOURCE_PACKAGE_FILE_NAME ${PROJECT_NAME}-${CPACK_PACKAGE_VERSION_MAJOR}.${CPACK_PACKAGE_VERSION_MINOR})
set(CPACK_SOURCE_GENERATOR "TGZ")
set(CPACK_SOURCE_IGNORE_FILES "doc/out;\.git;\.idea;CMakeLists\.txt\.user")

include(CPack)
