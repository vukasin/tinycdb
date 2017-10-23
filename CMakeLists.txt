cmake_minimum_required(VERSION 3.0.2)
project(tinyCDB VERSION 0.78 LANGUAGES C)

set(CMAKE_LD_FLAGS "-Wl,--no-as-needed")
add_compile_options(-msse4.2 -fPIC -Wno-deprecated)
add_definitions(-D_FILE_OFFSET_BITS=64)
option(BUILD_NSS "Enable building NSS components" OFF)

set(LIB_SOURCE_FILES cdb.c
    cdb_find.c
    cdb_findnext.c
    cdb_hash.c
    cdb_init.c
    cdb_make.c
    cdb_make_add.c
    cdb_make_put.c
    cdb_seek.c
    cdb_seq.c
    cdb_unpack.c
    )

set(NSS_SOURCE_FILES
    nss_cdb-group.c
    nss_cdb-passwd.c
    nss_cdb-spwd.c
    nss_cdb.c
    )

set(LIB_HEADERS cdb.h cdb_int.h)
set(NSS_HEADERS nss_cdb.h)

add_library(cdb STATIC ${LIB_SOURCE_FILES})
set_target_properties(cdb PROPERTIES
    SOVERSION ${PROJECT_VERSION_MAJOR}
    VERSION ${PROJECT_VERSION}
    PUBLIC_HEADER "${LIB_HEADERS}"
    OUTPUT_NAME cdb)
if(${BUILD_NSS})
    add_library(cdb-nss STATIC ${NSS_SOURCE_FILES})
    set_target_properties(cdb-nss PROPERTIES
        SOVERSION ${PROJECT_VERSION_MAJOR}
        VERSION ${PROJECT_VERSION}
        PUBLIC_HEADER "${NSS_HEADERS}"
        OUTPUT_NAME cdb-nss)

    install(TARGETS cdb-nss
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        PUBLIC_HEADER DESTINATION "include/cdb")

endif()

install(TARGETS cdb
    ARCHIVE DESTINATION lib
    LIBRARY DESTINATION lib
    PUBLIC_HEADER DESTINATION "include/cdb")

