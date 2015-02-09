cmake_minimum_required (VERSION 2.8.11)
project("libiconv")
include(GenerateExportHeader)

set(LIBICONV_VERSION_MAJOR 1)
set(LIBICONV_VERSION_MINOR 14)

set(LIBICONV_HEADERS
    "${PROJECT_BINARY_DIR}/iconv.h"
    "${PROJECT_BINARY_DIR}/iconv_export.h")

set(LIBICONV_SOURCES
    ../lib/iconv.c
    ../lib/relocatable.c
    ../libcharset/lib/localcharset.c)

if(MSVC)
set(CMAKE_C_FLAGS "/wd4018")
endif()

include_directories("${PROJECT_BINARY_DIR}")

set(DLL_VARIABLE "")
set(EILSEQ EILSEQ)
set(ICONV_CONST const)
set(USE_MBSTATE_T 0)
set(BROKEN_WCHAR_H 0)
set(HAVE_WCHAR_T 0)

add_definitions(-DBUILDING_LIBICONV -DBUILDING_LIBCHARSET)
add_definitions(-DLIBCHARSET_DLL_EXPORTED=LIBICONV_DLL_EXPORTED)
add_definitions(-DLIBDIR)

configure_file(iconv.h.in "${PROJECT_BINARY_DIR}/iconv.h")
configure_file(config.h.in "${PROJECT_BINARY_DIR}/config.h")
configure_file(localcharset.h.in "${PROJECT_BINARY_DIR}/localcharset.h")

add_library(iconv SHARED ${LIBICONV_SOURCES})

GENERATE_EXPORT_HEADER(iconv
    BASE_NAME LIBICONV
    EXPORT_MACRO_NAME LIBICONV_DLL_EXPORTED
    EXPORT_FILE_NAME iconv_export.h
    STATIC_DEFINE LIBICONV_BUILT_AS_STATIC)

install (FILES ${LIBICONV_HEADERS} DESTINATION include)
install(TARGETS iconv
    RUNTIME DESTINATION bin
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib)
