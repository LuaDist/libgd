include_directories ( BEFORE "${GD_SOURCE_DIR}/src" "${CMAKE_BINARY_DIR}" )

set ( TESTS_FILES #  resize
  #  ellipseaa
  #  ellipse
  #  ellipsearc
  #  ellfullaa
  # nnquant
  gif )
if ( PNG_FOUND )
  set ( TESTS_FILES "${TESTS_FILES}" arc crop tgaread )
endif ( )

if ( JPEG_FOUND )
  set ( TESTS_FILES "${TESTS_FILES}" nnquant copyrotated flip )
endif ( JPEG_FOUND )

if ( TIFF_FOUND )
  set ( TESTS_FILES "${TESTS_FILES}" tiffread )
endif ( TIFF_FOUND )

foreach ( test_name ${TESTS_FILES} )
	add_executable ( ${test_name} "${test_name}.c" )
	target_link_libraries ( ${test_name} ${GD_LIB} )
	# LuaDist: added instalation of examples:
	install ( TARGETS ${test_name} DESTINATION ${INSTALL_EXAMPLE} COMPONENT Example )
endforeach ( test_name )

if ( WIN32 AND NOT MINGW AND NOT MSYS )
  add_executable ( windows WIN32 "windows.c" )
  target_link_libraries ( windows ${GD_LIB} )
else ( WIN32 AND NOT MINGW AND NOT MSYS )
  if ( MINGW OR MSYS )
    add_executable ( windows "windows.c" )
    add_definitions ( "-mwindows" )
    target_link_libraries ( windows ${GD_LIB} stdc++ gdi32 )
  endif ( MINGW OR MSYS )
endif ( WIN32 AND NOT MINGW AND NOT MSYS )

# LuaDist: install sample images
install_example ( noIcon.pic test_crop_threshold.png noIcon.sgi noIcon.tga noIconAlpha.tga  )
