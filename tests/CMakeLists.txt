if ( BUILD_TEST )
  set ( DART_TESTING_TIMEOUT 5 )

  include ( Dart )

  set ( GDTEST_SOURCE_DIR ${GD_SOURCE_DIR}/tests/gdtest )

  if ( ENABLE_GCOV AND CMAKE_COMPILER_IS_GNUCC )
    set ( CMAKE_BUILD_TYPE TESTINGGCOV )
    set ( CMAKE_CXX_FLAGS_TESTINGGCOV "-g3 -O0" )
    set ( CMAKE_C_FLAGS_TESTINGGCOV "-g3 -O0" )
  endif ( ENABLE_GCOV AND CMAKE_COMPILER_IS_GNUCC )

  message ( STATUS "gd include dir: ${GD_INCLUDE_DIR}" )
  message ( STATUS "gd libs dir: ${GD_LIBS_DIR}" )

  include_directories ( BEFORE ${GD_INCLUDE_DIR} "${GDTEST_SOURCE_DIR}" )
  link_directories ( "${GD_LIBS_DIR}" "${GDTEST_BINARY_DIR}/gdtest" )

  include_directories ( BEFORE ${GD_INCLUDE_DIR} "${GDTEST_SOURCE_DIR}" "${CMAKE_BINARY_DIR}" 
    "${CMAKE_BINARY_DIR}/tests/gdtest" )

  set ( TESTS_DIRS gdtest bmp freetype gd gd2 gdimagearc gdimagecolorclosest gdimagecolordeallocate 
    gdimagecolorexact gdimagecolorreplace gdimagecolorresolve gdimagecolortransparent 
    gdimagecopy gdimagecopyrotated #gdimageellipse
    gdimagefill gdimagefilledellipse gdimagefilledpolygon gdimagefilledrectangle 
    gdimagefilltoborder gdimageline gdimageopenpolygon gdimagepixelate gdimagepolygon 
    gdimagerectangle gdimagescatterex gdimagesetpixel gdimagestringft gdimagestringftex 
    gdnewfilectx gdtiled gif jpeg png tga tiff wbmp xpm )

  if ( WIN32 )
    set ( GDTESTS_TARGET_LINK "gdTest" )
  else ( WIN32 )
    set ( GDTESTS_TARGET_LINK "gdTest" )
  endif ( WIN32 )

  foreach ( test_dir ${TESTS_DIRS} )
  add_subdirectory ( ${test_dir} )
  endforeach ( test_dir )

endif ( BUILD_TEST )
