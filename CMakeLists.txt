cmake_minimum_required(VERSION 2.6)
project(gptfdisk CXX)

# if unicode functionality is required
# use -DCURSES_NEED_WIDE=ON to enable this configuration
option(CURSES_NEED_WIDE "Build with unicode functionality" OFF)

include(GNUInstallDirs)
find_package(PkgConfig)
find_package(Curses)

configure_file("${CMAKE_CURRENT_SOURCE_DIR}/config.h.in"
        "${CMAKE_CURRENT_BINARY_DIR}/config.h")

pkg_check_modules(UUID uuid REQUIRED)
pkg_check_modules(POPT popt REQUIRED)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -D_FILE_OFFSET_BITS=64")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -D_FILE_OFFSET_BITS=64")

include_directories(${CMAKE_CURRENT_SOURCE_DIR} ${CMAKE_CURRENT_BINARY_DIR})

set(LIB_NAMES
    crc32.cc
    support.cc
    guid.cc
    gptpart.cc
    mbrpart.cc
    basicmbr.cc
    mbr.cc
    gpt.cc
    bsd.cc
    parttypes.cc
    attributes.cc
    diskio.cc
    diskio-unix.cc
)

set(MBR_LIBS
    support.cc
    diskio.cc
    diskio-unix.cc
    basicmbr.cc
    mbrpart.cc
)

add_executable(gdisk ${LIB_NAMES} gdisk.cc gpttext.cc)
target_link_libraries(gdisk ${UUID_LIBRARIES})
install (TARGETS gdisk DESTINATION ${CMAKE_INSTALL_BINDIR})

add_executable(cgdisk ${LIB_NAMES} cgdisk.cc gptcurses.cc)
target_link_libraries(cgdisk ${UUID_LIBRARIES} ${CURSES_LIBRARIES})
install (TARGETS cgdisk DESTINATION ${CMAKE_INSTALL_BINDIR})

add_executable(sgdisk ${LIB_NAMES} sgdisk.cc gptcl.cc)
target_link_libraries(sgdisk ${UUID_LIBRARIES} ${POPT_LIBRARIES})
install (TARGETS sgdisk DESTINATION ${CMAKE_INSTALL_BINDIR})

add_executable(fixparts ${MBR_LIBS} fixparts.cc)
install (TARGETS fixparts DESTINATION ${CMAKE_INSTALL_BINDIR})
