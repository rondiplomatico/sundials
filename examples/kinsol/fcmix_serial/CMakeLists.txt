# ---------------------------------------------------------------
# $Revision: 1.5 $
# $Date: 2009/02/17 02:58:47 $
# ---------------------------------------------------------------
# Programmer:  Radu Serban @ LLNL
# ---------------------------------------------------------------
# Copyright (c) 2007, The Regents of the University of California.
# Produced at the Lawrence Livermore National Laboratory.
# All rights reserved.
# For details, see the LICENSE file.
# ---------------------------------------------------------------
# CMakeLists.txt file for the FKINSOL serial examples


# Add variable kinsol_examples with the names of the serial FKINSOL examples

SET(FKINSOL_examples
  fkinDiagon_kry
  )

# Add variable FKINSOL_examples_BL with the names of the serial FKINSOL examples
# that use Lapack

SET(FKINSOL_examples_BL
  )

SET(KINSOL_LIB sundials_kinsol)
SET(NVECS_LIB sundials_nvecserial)
SET(FNVECS_LIB sundials_fnvecserial)

# Only static FCMIX libraries are available

SET(FKINSOL_LIB sundials_fkinsol)

# Set-up linker flags and link libraries

SET(SUNDIALS_LIBS ${FKINSOL_LIB} ${KINSOL_LIB} ${FNVECS_LIB} ${NVECS_LIB} ${EXTRA_LINK_LIBS})
IF(LAPACK_FOUND)
  SET(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${LAPACK_LINKER_FLAGS}")
  LIST(APPEND SUNDIALS_LIBS lapack)
ENDIF()

# Add the build and install targets for each FKINSOL example

FOREACH(example ${FKINSOL_examples})
  ADD_EXECUTABLE(${example} ${example}.f)
  TARGET_LINK_LIBRARIES(${example} ${SUNDIALS_LIBS})
  IF(EXAMPLES_INSTALL)
    INSTALL(FILES ${example}.f ${example}.out DESTINATION ${EXAMPLES_INSTALL_PATH}/kinsol/fcmix_serial)
  ENDIF()
ENDFOREACH(example ${FKINSOL_examples})

# If Lapack support is enabled, add the build and install targets for
# the examples using Lapack

IF(LAPACK_FOUND)
  FOREACH(example ${FKINSOL_examples_BL})
    ADD_EXECUTABLE(${example} ${example}.f)
    TARGET_LINK_LIBRARIES(${example} ${SUNDIALS_LIBS})
    IF(EXAMPLES_INSTALL)
      INSTALL(FILES ${example}.f ${example}.out DESTINATION ${EXAMPLES_INSTALL_PATH}/kinsol/fcmix_serial)
    ENDIF()
  ENDFOREACH(example ${FKINSOL_examples_BL})
ENDIF()


IF(EXAMPLES_INSTALL)

  # Install the README file
  INSTALL(FILES README DESTINATION ${EXAMPLES_INSTALL_PATH}/kinsol/fcmix_serial)

  # Prepare substitution variables for Makefile and/or CMakeLists templates
  SET(SOLVER "KINSOL")
  SET(SOLVER_LIB "sundials_kinsol")
  SET(SOLVER_FLIB "sundials_fkinsol")
  LIST2STRING(FKINSOL_examples EXAMPLES)
  IF(LAPACK_FOUND)
    LIST2STRING(FKINSOL_examples_BL EXAMPLES_BL)
  ELSE()
    SET(EXAMPLES_BL "")
  ENDIF()

  # Regardless of the platform we're on, we will generate and install 
  # CMakeLists.txt file for building the examples. This file  can then 
  # be used as a template for the user's own programs.

  # generate CMakelists.txt in the binary directory
  CONFIGURE_FILE(
      ${PROJECT_SOURCE_DIR}/examples/templates/cmakelists_serial_F77_ex.in
      ${PROJECT_BINARY_DIR}/examples/kinsol/fcmix_serial/CMakeLists.txt
      @ONLY
      )

  # install CMakelists.txt
  INSTALL(
    FILES ${PROJECT_BINARY_DIR}/examples/kinsol/fcmix_serial/CMakeLists.txt
    DESTINATION ${EXAMPLES_INSTALL_PATH}/kinsol/fcmix_serial 
    )

  # On UNIX-type platforms, we also  generate and install a makefile for 
  # building the examples. This makefile can then be used as a template 
  # for the user's own programs.

  IF(UNIX)
    # generate Makefile and place it in the binary dir
    CONFIGURE_FILE(
      ${PROJECT_SOURCE_DIR}/examples/templates/makefile_serial_F77_ex.in
      ${PROJECT_BINARY_DIR}/examples/kinsol/fcmix_serial/Makefile_ex
      @ONLY
      )
    # install the configured Makefile_ex as Makefile
    INSTALL(
      FILES ${PROJECT_BINARY_DIR}/examples/kinsol/fcmix_serial/Makefile_ex 
      DESTINATION ${EXAMPLES_INSTALL_PATH}/kinsol/fcmix_serial 
      RENAME Makefile
      )
  ENDIF()

ENDIF()
