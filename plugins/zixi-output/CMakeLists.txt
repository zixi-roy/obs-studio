project(zixi-output)
include (../../cmake/external/ObsPluginHelpers.cmake)
include_directories(${OBS_JANSSON_INCLUDE_DIRS})

set(zixi-output_SOURCES
	zixi-output.c
	zixi-output-main.c)

set(zixi-output_HEADERS
	include/zixi_feeder_interface.h
	zixi-constants.h
	zixi-dynload.h
	)

if (WIN32)
	if (CMAKE_SIZEOF_VOID_P EQUAL 8)
		set(ZIXI_INTERN_LIB_NAME feeder_interface-x64)
	else(CMAKE_SIZEOF_VOID_P EQUAL 8)
		set(ZIXI_INTERN_LIB_NAME feeder_interface-Win32)
	endif(CMAKE_SIZEOF_VOID_P EQUAL 8)
	set (ZIXI_INTERN_LIB_FULL_NAME "${ZIXI_INTERN_LIB_NAME}.dll")
elseif(APPLE)
	set(ZIXI_INTERN_LIB_NAME zixiFeeder)
	if (CMAKE_SIZEOF_VOID_P EQUAL 8)
		set (ZIXI_INTERN_LIB_PATH "${ZIXI_INTERN_LIB_PATH}lib/mac/64")
	else (CMAKE_SIZEOF_VOID_P EQUAL 8)
		set (ZIXI_INTERN_LIB_PATH lib/mac/32)
	endif (CMAKE_SIZEOF_VOID_P EQUAL 8)
	set (ZIXI_INTERN_LIB_FULL_NAME "lib${ZIXI_INTERN_LIB_NAME}.dylib")
else(UNIX)
	set (ZIXI_INTERN_LIB_NAME zixiFeeder)
	if (CMAKE_SIZEOF_VOID_P EQUAL 8)
			set (ZIXI_INTERN_LIB_PATH lib/linux/64)
	elseif(CMAKE_SIZEOF_VOID_P EQUAL 8)
			set (ZIXI_INTERN_LIB_PATH lib/linux/32)
	endif(CMAKE_SIZEOF_VOID_P EQUAL 8)
	set (ZIXI_INTERN_LIB_FULL_NAME "lib${ZIXI_INTERN_LIB_NAME}.so")
endif()

add_library(zixi-output MODULE
	${zixi-output_SOURCES}
	${zixi-output_HEADERS})
	
target_link_libraries(zixi-output
	libobs
	file-updater
	${OBS_JANSSON_IMPORT})


target_compile_definitions(zixi-output PRIVATE "-DZIXI_DLL_NAME=\"${ZIXI_INTERN_LIB_FULL_NAME}\"")
	
install_obs_plugin (zixi-output)

