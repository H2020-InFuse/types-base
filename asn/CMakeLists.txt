# H2020 ESROCOS Project
# Company: GMV Aerospace & Defence S.A.U.
# Licence: GPLv2

esrocos_asn1_types_package(esrocos_types_base_asn 
        Angle.asn
        Array3D.asn
        BodyState.asn
        Covariance.asn
        Eigen.asn
        JointLimitRange.asn
        JointLimits.asn
        Joints.asn
        JointState.asn
        JointsTrajectory.asn
        Motion2D.asn
        NamedVector.asn
        Point.asn
        Pose.asn
        Pressure.asn
        RigidBodyAcceleration.asn
        RigidBodyState.asn
        taste-extended.asn
        taste-types.asn
        Temperature.asn
        Time.asn
        Trajectory.asn
        TransformWithCovariance.asn
        TwistWithCovariance.asn
        Waypoint.asn
        Wrench.asn
        Wrenches.asn)

#esrocos_asn1_types_build_test(esrocos_types_base_asn)
esrocos_asn1_types_install(esrocos_types_base_asn ${CMAKE_INSTALL_PREFIX}/types/base)

#now build a library and install headers:

# ASN.1 types are imported and compiled locally to the project, but not installed
# esrocos_asn1_types_package(esrocos_types_base
#     OUTDIR asn1
#     IMPORT types/base)

#add_custom_target(asnlib ALL)
#add_dependencies(asnlib esrocos_types_base_generate_c)

#install(DIRECTORY ${CMAKE_BINARY_DIR}/asn/asn1 DESTINATION ${CMAKE_INSTALL_PREFIX}/include)
#install(TARGETS esrocos_types_base_asn)






