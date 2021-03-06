# vim: syntax=cmake
IF(POLICY CMP0025)
    CMAKE_POLICY(SET CMP0025 OLD) # report Apple's Clang as just Clang
ENDIF()
IF(POLICY CMP0042)
    CMAKE_POLICY(SET CMP0042 NEW) # MACOSX_RPATH
ENDIF()
IF(CMAKE_GENERATOR STREQUAL "Xcode")
    SET(XCODE 1)
ENDIF()
IF(${XCODE})
	SET( SYS_LIB /usr/lib /usr/local/lib )
ENDIF()

CMAKE_MINIMUM_REQUIRED(VERSION 2.8)
PROJECT( AVCAPTURE )
FIND_PACKAGE( OpenCV REQUIRED )
INCLUDE_DIRECTORIES( ${OpenCV_INCLUDE_DIRS} )
IF(${UNIX})
	SET( CMAKE_BUILD_TYPE Release )
ENDIF()
configure_file("avcapture.pc.in" "avcapture.pc" @ONLY)
install(FILES       "${CMAKE_CURRENT_BINARY_DIR}/avcapture.pc"
        DESTINATION lib/pkgconfig)
SET( MAKE_NAN_SOLUTION OFF )
SET( CMAKE_INSTALL_PREFIX /usr/local )
SET( main_SRCS main.cpp avcapture.cpp avcapture.h )

LINK_DIRECTORIES( ${SYS_LIB} )
ADD_EXECUTABLE(sample  ${main_SRCS} )
TARGET_LINK_LIBRARIES( sample ${OpenCV_LIBS} )
TARGET_LINK_LIBRARIES( sample avformat avcodec avutil swscale avdevice )

ADD_EXECUTABLE(sample2  sample2.cpp )
TARGET_LINK_LIBRARIES( sample2 ${OpenCV_LIBS} )
TARGET_LINK_LIBRARIES( sample2 avcapture )

ADD_LIBRARY(avcapture SHARED avcapture.cpp)
TARGET_LINK_LIBRARIES( avcapture ${OpenCV_LIBS} )
TARGET_LINK_LIBRARIES( avcapture avformat avcodec avutil swscale avdevice )
SET_TARGET_PROPERTIES( avcapture  PROPERTIES VERSION 1 )

INSTALL( TARGETS avcapture LIBRARY DESTINATION lib/ )
INSTALL( FILES avcapture.h DESTINATION include/ )

IF(${MAKE_NAN_SOLUTION})
    ADD_SUBDIRECTORY(./node-addon)
ENDIF()
