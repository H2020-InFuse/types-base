# H2020 ESROCOS Project
# Company: GMV Aerospace & Defence S.A.U.
# Licence: GPLv2


cmake_minimum_required(VERSION 3.3)
# required version 3.3: provides "IN_LIST" operator in if()

project(esrocos_types_base)

add_subdirectory(asn1)

install(DIRECTORY ${CMAKE_SOURCE_DIR}/asn1
        DESTINATION ${CMAKE_INSTALL_PREFIX}/${CMAKE_PROJECT_NAME})

