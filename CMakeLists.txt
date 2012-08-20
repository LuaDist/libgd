# This is a slightly modified original CMakeLists.txt file to suit LuaDist needs. 

project ( gd )
cmake_minimum_required ( VERSION 2.8 )

set ( GD_SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR} )

set ( PACKAGE GD )
set ( PACKAGE_NAME GD )
set ( CMAKE_MODULE_PATH "${GD_SOURCE_DIR}/cmake/modules" )

include ( cmake/dist.cmake )

option ( ENABLE_PNG "Enable PNG support" 1 )
option ( ENABLE_JPEG "Enable JPEG support" 1 )
option ( ENABLE_TIFF "Enable TIFF support" 1 )
option ( ENABLE_XPM "Enable XPM support" 1 )
option ( ENABLE_FREETYPE "Enable Freetype2 support" 1 )
option ( ENABLE_WBMP "Enable WBMP support" 1 )
option ( ENABLE_FONTCONFIG "Enable FontConfig support" 1 )

if ( BUILD_TEST )
  enable_testing ( )
endif ( BUILD_TEST )

if ( NOT WIN32 )
  if ( CMAKE_BUILD_TYPE )
    string ( TOLOWER ${CMAKE_BUILD_TYPE} CMAKE_BUILD_TYPE_TOLOWER )
    if ( CMAKE_BUILD_TYPE_TOLOWER MATCHES debug )
      set ( CMAKE_C_FLAGS_DEBUG "${CMAKE_C_FLAGS_DEBUG} -Wall -O0" )
    endif ( CMAKE_BUILD_TYPE_TOLOWER MATCHES debug )
  endif ( CMAKE_BUILD_TYPE )
endif ( NOT WIN32 )

if ( USE_EXT_GD )
  message ( "Using GD at: ${USE_EXT_GD}" )
  include_directories ( BEFORE ${GD_INCLUDE_DIR} )
  find_package ( GD )
  if ( GD_FOUND )
    include_directories ( BEFORE ${GD_INCLUDE_DIR} )
    link_directories ( BEFORE ${GD_LIBRARIES} )
    set ( GD_LIB ${GD_LIBRARIES} )
    set ( GD_LIBS_DIR ${GD_LIBRARY} )

    message ( "GD libs #: ${GD_LIBRARIES}" )
    message ( "GD lib #: ${GD_LIBRARY}" )
    message ( "GD include: ${GD_INCLUDE_DIR}" )
  else ( GD_FOUND )
    message ( "No gd found" )
  endif ( GD_FOUND )
else ( USE_EXT_GD )

  set ( GD_VERSION_MAJOR "2" )
  set ( GD_VERSION_MINOR "1" )
  set ( GD_VERSION_PATCH "0" )
  set ( GD_VERSION "${GD_VERSION_MAJOR}.${GD_VERSION_MINOR}.${GD_VERSION_PATCH}" )
  set ( GD_VERSION_STRING "${GD_VERSION}" )

  set ( GD_VERSION_INT "20100" )

  set ( CMAKE_REQUIRED_INCLUDES "/usr/include" "/usr/local/include" )

  include ( CheckIncludeFiles )
  include ( CheckIncludeFile )

  include ( AC_HEADER_STDC )
  include ( CheckPrototypeExists )
  set ( FONTCONFIG_FIND_QUIETLY, 1 )

  message ( STATUS "PNG Support: ${ENABLE_PNG}" )

  if ( ENABLE_PNG )
    find_package ( PNG REQUIRED )
  endif ( ENABLE_PNG )

  find_package ( ZLIB )

  if ( NOT WIN32 )
    find_package ( PTHREAD )
  endif ( NOT WIN32 )

  find_package ( Freetype )
  find_package ( XPM )

  if ( ENABLE_JPEG )
    find_package ( JPEG )
  endif ( ENABLE_JPEG )

  if ( ENABLE_TIFF )
    find_package ( TIFF )
  endif ( ENABLE_TIFF )

  if ( NOT WIN32 )
    find_package ( PTHREAD )
  endif ( NOT WIN32 )

  if ( ENABLE_FREETYPE )
    find_package ( Freetype )
  endif ( ENABLE_FREETYPE )

  if ( ENABLE_XPM )
    find_package ( XPM )
  endif ( ENABLE_XPM )

  if ( ENABLE_FONTCONFIG )
    find_package ( Fontconfig )
  endif ( ENABLE_FONTCONFIG )

  if ( FREETYPE_FOUND )
    include_directories ( ${FREETYPE_INCLUDE_DIR} )
    set ( HAVE_FT2BUILD_H 1 )
    set ( HAVE_LIBFREETYPE 1 )
  endif ( FREETYPE_FOUND )

  if ( ZLIB_FOUND )
    include_directories ( ${PNG_INCLUDE_DIR} )
    set ( HAVE_LIBZ 1 )
    set ( HAVE_LIBPNG_PNG_H 1 )
  endif ( ZLIB_FOUND )

  if ( PNG_FOUND )
    include_directories ( ${PNG_INCLUDE_DIR} )
    set ( HAVE_LIBPNG 1 )
  endif ( PNG_FOUND )

  if ( XPM_FOUND )
    include_directories ( ${XPM_INCLUDE_DIR} )
    set ( HAVE_LIBXPM 1 )
  endif ( XPM_FOUND )

  if ( JPEG_FOUND )
    include_directories ( ${JPEG_INCLUDE_DIR} )
    set ( HAVE_LIBJPEG 1 )
  endif ( JPEG_FOUND )

  if ( TIFF_FOUND )
    include_directories ( ${TIFF_INCLUDE_DIR} )
    set ( HAVE_LIBTIFF 1 )
  endif ( TIFF_FOUND )

  if ( FONTCONFIG_FOUND )
    include_directories ( ${FONTCONFIG_INCLUDE_DIR} )
    set ( HAVE_LIBFONTCONFIG 1 )
  else ( FONTCONFIG_FOUND )
    set ( FONTCONFIG_LIBRARIES "" )
    set ( FONTCONFIG_INCLUDE_DIR "" )
    set ( FONTCONFIG_LIB_DIR "" )
  endif ( FONTCONFIG_FOUND )

  set ( HAVE_CONFIG_H 1 )

  add_definitions ( -DHAVE_CONFIG_H )

  configure_file ( ${GD_SOURCE_DIR}/src/config.h.cmake ${CMAKE_BINARY_DIR}/config.h 
    ESCAPE_QUOTES )

  set ( BUILD_SHARED_LIBS On )

  if ( WIN32 )
    set ( GD_LIB bgd )
    add_definitions ( -DWIN32 -D_WIN32 -DMSWIN32 -DBGDWIN32 -DWINVER=0x0500 -D_WIN32_WINNT=0x0500 
      -D_WIN32_IE=0x0600 )

    if ( NOT MINGW AND MSVC_VERSION GREATER 1399 )
      add_definitions ( "/D_CRT_SECURE_NO_DEPRECATE" )
    endif ( NOT MINGW AND MSVC_VERSION GREATER 1399 )
    if ( MINGW OR MSYS )
      add_definitions ( "-mms-bitfields -m32" )
    endif ( MINGW OR MSYS )
  else ( WIN32 )
    set ( GD_LIB gd )
  endif ( WIN32 )

  set ( GD_LIB_STATIC "${GD_LIB}-static" )

  if ( PROFILE )
    add_definitions ( "-pg" )
    set ( CMAKE_EXE_LINKER_FLAGS ${LINK_FLAGS} "-pg" )
  endif ( PROFILE )

  set ( GD_INCLUDE_DIR "${GD_SOURCE_DIR}/src" "${GD_SOURCE_DIR}" )
  include_directories ( before ${FONTCONFIG_INCLUDE_DIR} )

  add_subdirectory ( src )
endif ( USE_EXT_GD )

add_subdirectory ( tests )
add_subdirectory ( examples )

install_doc ( ${top_level_DOCFILES} )

install_data ( ChangeLog NEWS docs/README.TXT docs/README-JPEG.TXT docs/README.JPN 
  docs/README.TESTING )
