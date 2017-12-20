set(ASN1
    ${CMAKE_CURRENT_SOURCE_DIR}/base/Angle.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/base/BodyState.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/base/Covariance.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/base/Eigen.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/base/JointLimitRange.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/base/JointLimits.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/base/Joints.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/base/JointState.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/base/JointsTrajectory.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/base/Motion2D.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/base/NamedVector.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/base/Point.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/base/Pose.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/base/Pressure.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/base/RigidBodyAcceleration.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/base/RigidBodyState.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/base/taste-extended.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/base/taste-types.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/base/Temperature.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/base/Time.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/base/Trajectory.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/base/TransformWithCovariance.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/base/TwistWithCovariance.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/base/Waypoint.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/base/Wrench.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/base/Wrenches.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/drivers/CanMessage.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/drivers/CanOpen.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/drivers/JoystickCommand.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/drivers/pohicdriver-can.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/drivers/pohicdriver-ip.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/drivers/TimestampEstimatorStatus.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/sensor_samples/CompressedFrame.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/sensor_samples/DepthMap.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/sensor_samples/DistanceImage.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/sensor_samples/Frame.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/sensor_samples/IMUSensors.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/sensor_samples/LaserScan.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/sensor_samples/Pointcloud.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/sensor_samples/Sonar.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/sensor_samples/SonarBeam.asn
    ${CMAKE_CURRENT_SOURCE_DIR}/sensor_samples/SonarScan.asn
)


# First compilation, needed to define library build targets

set(ASN1_OUT_DIR ${CMAKE_CURRENT_BINARY_DIR})

execute_process(
    COMMAND asn1.exe -c -typePrefix asn1Scc -uPER -wordSize 8 -ACN -o ${ASN1_OUT_DIR} -atc ${ASN1}
    RESULT_VARIABLE ASN1SCC_RESULT
)

if(${ASN1SCC_RESULT} EQUAL 0)
    message(STATUS "ASN.1 first compilation successful.")
else()
    message(FATAL_ERROR "ASN.1 first compilation failed.")
endif()


# Command for C compilation; creates timestamp file
add_custom_command(OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/asn1-stamp
    COMMAND asn1.exe -c -typePrefix asn1Scc -uPER -wordSize 8 -ACN -o ${ASN1_OUT_DIR} -atc ${ASN1}
    COMMAND ${CMAKE_COMMAND} -E touch ${CMAKE_CURRENT_BINARY_DIR}/pus-asn1-stamp
    DEPENDS ${ASN1}
    COMMENT "Generate header files for: ${ASN1} in ${ASN1_OUT_DIR}"
)


# Target for C compilation; uses stamp file to run dependent targets only if changed
add_custom_target(
    esrocos_types_base_generate_c
    DEPENDS ${CMAKE_CURRENT_BINARY_DIR}/asn1-stamp
)

# Common files created by the ASN.1 compiler
set(ASN1SCC_COMMON
    ${ASN1_OUT_DIR}/acn.c
    ${ASN1_OUT_DIR}/asn1crt.c
    ${ASN1_OUT_DIR}/real.c
)

# Test files created by the ASN.1 compiler
set(ASN1SCC_TEST
    ${ASN1_OUT_DIR}/testsuite.c
    ${ASN1_OUT_DIR}/testsuite.h
    ${ASN1_OUT_DIR}/mainprogram.c
)

# Get generated .c files 
file(GLOB C_FILES "${ASN1_OUT_DIR}/*.c")

# Build list of sources excluding the files generated for unit tests (-atc)
foreach(cFile ${C_FILES})
    get_filename_component(fName ${cFile} NAME)
    if(NOT ${ASN1_OUT_DIR}/${fName} IN_LIST ASN1SCC_COMMON 
       AND NOT ${ASN1_OUT_DIR}/${fName} IN_LIST ASN1SCC_TEST 
       AND NOT ${fName} MATCHES "^.*_auto_tcs.c$")
            list(APPEND SOURCES ${cFile})
    endif()
endforeach()

# Define the types library
add_library(esrocos_types_base STATIC ${SOURCES})
set_target_properties(esrocos_types_base PROPERTIES LINKER_LANGUAGE C)
set_target_properties(esrocos_types_base PROPERTIES POSITION_INDEPENDENT_CODE TRUE)
add_dependencies(esrocos_types_base esrocos_types_base_generate_c)

# Create a library with the common .c files
add_library(esrocos_types_asn1common STATIC ${ASN1SCC_COMMON})
set_target_properties(esrocos_types_asn1common PROPERTIES LINKER_LANGUAGE C)
set_target_properties(esrocos_types_asn1common PROPERTIES POSITION_INDEPENDENT_CODE TRUE)
add_dependencies(esrocos_types_asn1common esrocos_types_base_generate_c)

# Unit tests executable
add_executable(test_esrocos_types_base ${C_FILES})

# Install headers and libraries
install(DIRECTORY ${ASN1_OUT_DIR}
        DESTINATION ${CMAKE_INSTALL_PREFIX}/include/types/${CMAKE_PROJECT_NAME}
        FILES_MATCHING
        PATTERN "*.h"
        PATTERN "*_auto_tcs.h" EXCLUDE
        PATTERN "CMakeFiles" EXCLUDE
        )
        
install(TARGETS esrocos_types_base esrocos_types_asn1common
        DESTINATION ${CMAKE_INSTALL_PREFIX}/lib)
