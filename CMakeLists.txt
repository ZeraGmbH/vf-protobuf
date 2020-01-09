cmake_minimum_required(VERSION 3.1)

#set project name and language
project(vf-protobuf LANGUAGES CXX C)

#Automoc and RCC ON. Needed in QT projects
#set(CMAKE_AUTOMOC ON)
#set(CMAKE_AUTORCC ON)
#C++ 11 Flag
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

#Set project Version
set(VfProtobuf_VERSION_MAJOR "0")
set(VfProtobuf_VERSION_MINOR "0")
set(VfProtobuf_VERSION_PATCH "1")

set(PROJECT_VERSION "${VfProtobuf_VERSION_MAJOR}.${VfProtobuf_VERSION_MINOR}.${VfProtobuf_VERSION_PATCH}")

#Adding cmake functions like feature_summary()
include(FeatureSummary)
include(GNUInstallDirs)

#Find dependecies
find_package(Protobuf REQUIRED)


PROTOBUF_GENERATE_CPP(PROTO_SRCS PROTO_HDRS vfcore.proto)


#Find dependecies
file(GLOB SOURCES 
    ${PROJECT_SOURCE_DIR}/*.cpp
    ${CMAKE_CURRENT_BINARY_DIR}/*.cpp
    ${PROJECT_SOURCE_DIR}/*.cc
    ${CMAKE_CURRENT_BINARY_DIR}/*.cc
    )

set(SOURCES ${SOURCES}  ${PROTO_SRCS})

file(GLOB HEADER
    ${PROJECT_SOURCE_DIR}/*.h
    ${CMAKE_CURRENT_BINARY_DIR}/*.h
    )

set(HEADER ${HEADER}  ${PROTO_HDRS})

file(GLOB RESOURCES 
    ${PROJECT_SOURCE_DIR}/*.qrc
    )

#create library 
add_library(VfProtobuf SHARED
    ${SOURCES}
    ${HEADER}
    ${RESOURCES}
    )


#link dependencies
target_link_libraries(VfProtobuf
    PRIVATE
    ${Protobuf_LIBRARIES}
    )

# announce headers - target perspective
target_include_directories(VfProtobuf
    PUBLIC
    $<INSTALL_INTERFACE:include/VfProtobuf>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}>
    PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}
    )

#set target Version
set_target_properties(VfProtobuf PROPERTIES VERSION ${PROJECT_VERSION})
set_target_properties(VfProtobuf PROPERTIES SOVERSION ${VfProtobuf_VERSION_MAJOR})


#generate export header
include(GenerateExportHeader)
generate_export_header(VfProtobuf)

#install export header
install(FILES
    ${CMAKE_CURRENT_BINARY_DIR}/vfprotobuf_export.h
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/VfProtobuf
    )



# install library
install(TARGETS VfProtobuf
    EXPORT VfProtobufExport
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    )

# configure MarkdownQtConfigVersion.cmake
include(CMakePackageConfigHelpers)
write_basic_package_version_file(
    VfProtobufConfigVersion.cmake
    VERSION ${PACKAGE_VERSION}
    COMPATIBILITY SameMinorVersion
    )

# configure MarkdownQtConfig.cmake
configure_file(VfProtobufConfig.cmake.in VfProtobufConfig.cmake @ONLY)


# install MarkdownQtConfig(Version).cmake
install(FILES "${CMAKE_CURRENT_BINARY_DIR}/VfProtobufConfig.cmake"
    "${CMAKE_CURRENT_BINARY_DIR}/VfProtobufConfigVersion.cmake"
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/VfProtobuf
    )

# install targets cmake-files
install(EXPORT VfProtobufExport
    FILE VfProtobufTargets.cmake
    NAMESPACE VeinMeta::
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/VfProtobuf
    )

# install public headers
install(
    FILES ${HEADER}
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/VfProtobuf
    )

# spawn out some info on configuration
feature_summary(WHAT ALL FATAL_ON_MISSING_REQUIRED_PACKAGES)



