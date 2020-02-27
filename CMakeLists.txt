cmake_minimum_required(VERSION 3.1)
project(gptfdisk LANGUAGES CXX VERSION 1.0.5)

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

include_directories(${CMAKE_CURRENT_SOURCE_DIR} ${CMAKE_CURRENT_BINARY_DIR})

if(${CMAKE_SYSTEM_NAME} MATCHES "Windows")
    set(CMAKE_C_FLAGS ${CMAKE_C_FLAGS} "-O2 -Wall -static -static-libgcc -static-libstdc++  -D_FILE_OFFSET_BITS=64 -g")
    set(CMAKE_CXX_FLAGS ${CMAKE_CXX_FLAGS} "-O2 -Wall -static -static-libgcc -static-libstdc++ -D_FILE_OFFSET_BITS=64 -g")
    set(DISKIO diskio-windows.cc)
else()
    set(CMAKE_C_FLAGS ${CMAKE_C_FLAGS} "-D_FILE_OFFSET_BITS=64")
    set(CMAKE_CXX_FLAGS ${CMAKE_CXX_FLAGS} "-Wall -D_FILE_OFFSET_BITS=64")
    set(DISKIO diskio-unix.cc)
endif()

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
    ${DISKIO}
)

set(MBR_LIBS
    support.cc
    diskio.cc
    ${DISKIO}
    basicmbr.cc
    mbrpart.cc
)

add_executable(gdisk ${LIB_NAMES} gdisk.cc gpttext.cc)
target_link_libraries(gdisk ${UUID_LIBRARIES})
install (TARGETS gdisk DESTINATION ${CMAKE_INSTALL_BINDIR})


if(NOT ${CMAKE_SYSTEM_NAME} MATCHES "Windows")
    add_executable(cgdisk ${LIB_NAMES} cgdisk.cc gptcurses.cc)
    target_link_libraries(cgdisk ${UUID_LIBRARIES} ${CURSES_LIBRARIES})
    install (TARGETS cgdisk DESTINATION ${CMAKE_INSTALL_BINDIR})
endif()

add_executable(sgdisk ${LIB_NAMES} sgdisk.cc gptcl.cc)
target_link_libraries(sgdisk ${UUID_LIBRARIES} ${POPT_LIBRARIES})
install (TARGETS sgdisk DESTINATION ${CMAKE_INSTALL_BINDIR})

add_executable(fixparts ${MBR_LIBS} fixparts.cc)
install (TARGETS fixparts DESTINATION ${CMAKE_INSTALL_BINDIR})
