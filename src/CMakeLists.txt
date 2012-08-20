set ( LIBGD_SRC_FILES gd.c gdfx.c gdfx.h gd_color.c gd_color.h gd_color_map.c gd_color_map.h 
  gd_crop.c gd_transform.c gd_security.c gdpp.cxx gdpp.h gd_bmp.c bmp.h gd_filter.c 
  gd_gd.c gd_gd2.c gd_io.c gd_io.h gd_io_stream.cxx gd_io_stream.h gd_io_dp.c gd_gif_in.c 
  gd_gif_out.c gd_io_file.c gd_io_ss.c gd_jpeg.c gd_nnquant.c gd_nnquant.h gd_pixelate.c 
  gd_png.c gd_tiff.c gd_tga.c gd_tga.h gd_ss.c gd_topal.c gd_wbmp.c gdcache.c gdcache.h 
  gdfontg.c gdfontg.h gdfontl.c gdfontl.h gdfontmb.c gdfontmb.h gdfonts.c gdfonts.h 
  gdfontt.c gdfontt.h gdft.c gdhelpers.c gdhelpers.h gdkanji.c gdtables.c gdxpm.c 
  jisx0208.h wbmp.c gd.h wbmp.h )

add_library ( ${GD_LIB} ${LIBGD_SRC_FILES} )
#~ add_library(${GD_LIB_STATIC} STATIC  ${LIBGD_SRC_FILES})	# LuaDist: we  build only dynamic

if ( WIN32 AND NOT MINGW AND NOT MSYS )
  #	SET_TARGET_PROPERTIES(${GD_LIB} PROPERTIES LINK_FLAGS "/NODEFAULTLIB:msvcrt.lib")
endif ( WIN32 AND NOT MINGW AND NOT MSYS )

if ( MINGW OR MSYS )
  add_definitions ( "-mms-bitfields" )
endif ( MINGW OR MSYS )

include_directories ( ${CMAKE_BINARY_DIR}/ ${GD_SOURCE_DIR}/src )

target_link_libraries ( ${GD_LIB} ${FREETYPE_LIBRARIES} ${PNG_LIBRARIES} ${JPEG_LIBRARIES} 
  ${TIFF_LIBRARIES} ${XPM_LIBRARIES} ${FONTCONFIG_LIBRARIES} )
# LuaDist: we  build only dynamic:
# target_link_libraries(${GD_LIB_STATIC} ${FREETYPE_LIBRARIES} ${PNG_LIBRARIES} ${JPEG_LIBRARIES} ${TIFF_LIBRARIES} ${XPM_LIBRARIES} ${FONTCONFIG_LIBRARIES})

set ( GD_PROGRAMS annotate gdparttopng gdtopng gd2copypal gd2topng pngtogd pngtogd2 
  webpng gd2togif gdcmpgif giftogd2 )
foreach ( program ${GD_PROGRAMS} )
	add_executable ( ${program} ${program}.c )
	target_link_libraries ( ${program} ${GD_LIB} )
	install_executable( ${program} )
endforeach ( program )

# LuaDist: changed instalation paths:
install_library ( ${GD_LIB} )

install_header ( gd.h gdfx.h gd_io.h gdcache.h gdfontg.h gdfontl.h gdfontmb.h gdfonts.h 
  gdfontt.h gd_color_map.h )

# LuaDist: install additional stuff
install_doc ( entities.html )
