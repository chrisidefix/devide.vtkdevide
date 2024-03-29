CMAKE_MINIMUM_REQUIRED(VERSION 2.0)

#
# Set the project name.
#

PROJECT (VTKDEVIDE)

if(COMMAND cmake_policy)
    cmake_policy(SET CMP0003 NEW)
endif(COMMAND cmake_policy)

#
# Assume everything is set up correctly for build.
#
# If some requirements are not met, set it to 0.
# In that case, the build won't be done.
#

SET (VTKDEVIDE_CAN_BUILD 1)

#
# Load CMake commands that you probably should not modify.
#

INCLUDE (${VTKDEVIDE_SOURCE_DIR}/CMakeOptions.cmake)

#
# If everything is OK, then go into the sub directories and build.
#

IF (VTKDEVIDE_CAN_BUILD)

  #
  # Here is where you can list the sub-directories holding your local 
  # classes. Sorting classes by 'package' type like VTK does (Common, 
  # Rendering, Filtering, Imaging, IO, etc.) is a good thing and prevents 
  # numerous dependencies problems.
  #

  SUBDIRS (
    Common
    Hybrid
    Imaging
    Rendering
    External
  )

IF (${HAS_DCMTK})
  SUBDIRS (IO)
ENDIF (${HAS_DCMTK})

  #
  # You can put your include path(s) here
  #

  INCLUDE_DIRECTORIES(${VTKDEVIDE_SOURCE_DIR}/Common)
  INCLUDE_DIRECTORIES(${VTKDEVIDE_SOURCE_DIR}/External)
  INCLUDE_DIRECTORIES(${VTKDEVIDE_SOURCE_DIR}/Hybrid)
  INCLUDE_DIRECTORIES(${VTKDEVIDE_SOURCE_DIR}/Imaging)
  INCLUDE_DIRECTORIES(${VTKDEVIDE_SOURCE_DIR}/IO)
  INCLUDE_DIRECTORIES(${VTKDEVIDE_SOURCE_DIR}/Rendering)
  INCLUDE_DIRECTORIES(${VTKDEVIDE_SOURCE_DIR}/Testing)  
  
  #
  # are we going to build the examples?
  #
  
  OPTION(BUILD_EXAMPLES "Build examples." ON)
  IF (BUILD_EXAMPLES)
    SUBDIRS(Examples)
  ENDIF (BUILD_EXAMPLES)
  

  #
  # Common configuration settings
  #
  # Do not worry about this one.
  #

  CONFIGURE_FILE(
    ${VTKDEVIDE_SOURCE_DIR}/vtkdevideConfigure.h.in
    ${VTKDEVIDE_BINARY_DIR}/vtkdevideConfigure.h
  )

  INCLUDE_DIRECTORIES(${VTKDEVIDE_BINARY_DIR})

  # specify installation

  # first derive dir holding the DLLs
  IF(CMAKE_CONFIGURATION_TYPES)
   # on multi-config systems, e.g. win, we want bin/RelWthDebInfo 
   # I have to escape the ${BUILD_TYPE}, no idea why yet.
   SET(inst_from_dir ${LIBRARY_OUTPUT_PATH}/\${BUILD_TYPE})
  ELSE(CMAKE_CONFIGURATION_TYPES)
   # on single config systems, it's just bin
   SET(inst_from_dir ${LIBRARY_OUTPUT_PATH})
  ENDIF(CMAKE_CONFIGURATION_TYPES)

  # specify installation
  # copy all libs to the output dir in lib/; relative DESTINATION dir
  # is interpreted relative to CMAKE_INSTALL_PREFIX
  INSTALL(DIRECTORY ${inst_from_dir}/
      DESTINATION lib)
  # copy our python wrapping to the output dir in lib/
  INSTALL(FILES ${VTKDEVIDE_SOURCE_DIR}/Wrapping/Python/vtkdevide.py
      DESTINATION ${CMAKE_INSTALL_PREFIX}/lib)

ENDIF (VTKDEVIDE_CAN_BUILD)
