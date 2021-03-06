cmake_minimum_required(VERSION 3.5)
project(tinyxml_vendor)

list(INSERT CMAKE_MODULE_PATH 0 "${CMAKE_CURRENT_SOURCE_DIR}/cmake/Modules")
find_package(TinyXML)

if (NOT TinyXML_FOUND)
  include(ExternalProject)
  ExternalProject_Add(tinyxml-2.6.2
    PREFIX tinyxml-2.6.2
    URL https://superb-dca2.dl.sourceforge.net/project/tinyxml/tinyxml/2.6.2/tinyxml_2_6_2.tar.gz
    URL_MD5 c1b864c96804a10526540c664ade67f0
    CMAKE_ARGS
      -DCMAKE_INSTALL_PREFIX=${CMAKE_CURRENT_BINARY_DIR}/tinyxml_install
    INSTALL_DIR "${CMAKE_CURRENT_BINARY_DIR}/tinyxml_install"
    PATCH_COMMAND
    ${CMAKE_COMMAND} -E chdir <SOURCE_DIR> git apply -p1 --ignore-space-change --whitespace=nowarn ${CMAKE_CURRENT_SOURCE_DIR}/enforce-use-stl.patch
    COMMAND
    ${CMAKE_COMMAND} -E copy "${CMAKE_CURRENT_SOURCE_DIR}/tinyxml_cmakelists.txt" <SOURCE_DIR>/CMakeLists.txt
    )

  # The external project will install to the build folder, but we'll install that on make install.
  install(DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/tinyxml_install/ DESTINATION ${CMAKE_INSTALL_PREFIX})
else()
  message("Found TinyXML. Using TinyXML from system.")
endif()

configure_file(tinyxml_vendorConfig.cmake.in "${PROJECT_BINARY_DIR}/tinyxml_vendorConfig.cmake" @ONLY)
configure_file(tinyxml_vendorConfig-version.cmake.in "${PROJECT_BINARY_DIR}/tinyxml_vendorConfig-version.cmake" @ONLY)

install(DIRECTORY cmake DESTINATION share/${PROJECT_NAME})

install(FILES
  "${PROJECT_BINARY_DIR}/tinyxml_vendorConfig.cmake"
  "${PROJECT_BINARY_DIR}/tinyxml_vendorConfig-version.cmake"
  DESTINATION share/${PROJECT_NAME}/cmake)
