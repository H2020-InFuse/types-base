# H2020 ESROCOS Project
# Company: GMV Aerospace & Defence S.A.U.
# Licence: GPLv2

esrocos_asn1_types_package(esrocos_types_base 
        Angle.asn
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

esrocos_asn1_types_build_test(esrocos_types_base)
esrocos_asn1_types_install(esrocos_types_base ${CMAKE_INSTALL_PREFIX}/types/base)

