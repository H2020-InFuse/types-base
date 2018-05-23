# H2020 ESROCOS Project
# Company: GMV Aerospace & Defence S.A.U.
# Licence: GPLv2

esrocos_asn1_types_package(esrocos_types_base 
        base.asn        
        taste-extended.asn  
        taste-types.asn
        userdefs-base.asn
        )

esrocos_asn1_types_build_test(esrocos_types_base)
esrocos_asn1_types_install(esrocos_types_base ${CMAKE_INSTALL_PREFIX}/types/base)

