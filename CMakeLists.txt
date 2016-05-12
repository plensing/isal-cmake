cmake_minimum_required(VERSION 2.8.6)
project(isal-cmake C)
include(ExternalProject)

get_property(LIB64 GLOBAL PROPERTY FIND_LIBRARY_USE_LIB64_PATHS)
if ("${LIB64}" STREQUAL "TRUE")
    set(LIBSUFFIX 64)
else()
    set(LIBSUFFIX "")
endif()
message(STATUS "Library installation directory is set to ${CMAKE_INSTALL_PREFIX}/lib${LIBSUFFIX}")

ExternalProject_add(
        isal
        PREFIX intel
        URL "https://01.org/sites/default/files/downloads/intelr-storage-acceleration-library-open-source-version/isa-lopensrc2.13.tar.gz"
        URL_MD5 "f21ca06fc07857c71d574e59896c58d7"
        BUILD_IN_SOURCE 1
        CONFIGURE_COMMAND ""
        BUILD_COMMAND "make"
        INSTALL_COMMAND cp bin/libisal.so ${isal-cmake_BINARY_DIR}/libisal.so.2.13.0
        COMMAND ln -s libisal.so.2.13.0 ${isal-cmake_BINARY_DIR}/libisal.so.2
        COMMAND ln -s libisal.so.2 ${isal-cmake_BINARY_DIR}/libisal.so
)

install(FILES libisal.so libisal.so.2 libisal.so.2.13.0 DESTINATION lib${LIBSUFFIX})
install(DIRECTORY intel/src/isal/include/ DESTINATION include/isa-l)

set(CPACK_GENERATOR "RPM")
set(CPACK_PACKAGE_CONTACT "paul.lensing@gmail.com")
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "Intel Open Source Storage Accelartion Library")
set(CPACK_PACKAGE_DESCRIPTION "Intel Open Source Storage Accelartion Library")
set(CPACK_PACKAGE_VERSION "2.13.0")
set(CPACK_PACKAGE_NAME "isal")
set(CPACK_PACKAGE_FILE_NAME "${CPACK_PACKAGE_NAME}-${CPACK_PACKAGE_VERSION}")
include(CPack)