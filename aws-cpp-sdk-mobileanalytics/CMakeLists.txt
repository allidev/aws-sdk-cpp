cmake_minimum_required(VERSION 2.8.12)
project(aws-cpp-sdk-mobileanalytics)

  file(GLOB AWS_MOBILEANALYTICS_HEADERS
    "include/aws/mobileanalytics/*.h"
  )

  file(GLOB AWS_MOBILEANALYTICS_MODEL_HEADERS
    "include/aws/mobileanalytics/model/*.h"
  )

  file(GLOB AWS_MOBILEANALYTICS_SOURCE
    "source/*.cpp"
  )

  file(GLOB AWS_MOBILEANALYTICS_MODEL_SOURCE
    "source/model/*.cpp"
  )

  file(GLOB MOBILEANALYTICS_UNIFIED_HEADERS
    ${AWS_MOBILEANALYTICS_HEADERS}
    ${AWS_MOBILEANALYTICS_MODEL_HEADERS}
  )

  file(GLOB MOBILEANALYTICS_UNITY_SRC
    ${AWS_MOBILEANALYTICS_SOURCE}
    ${AWS_MOBILEANALYTICS_MODEL_SOURCE}
  )

  if(ENABLE_UNITY_BUILD)
    enable_unity_build("MOBILEANALYTICS" MOBILEANALYTICS_UNITY_SRC)
  endif()

  file(GLOB MOBILEANALYTICS_SRC
    ${MOBILEANALYTICS_UNIFIED_HEADERS}
    ${MOBILEANALYTICS_UNITY_SRC}
  )

  if(WIN32)
    #if we are compiling for visual studio, create a sane directory tree.
    if(MSVC)
      source_group("Header Files\\aws\\mobileanalytics" FILES ${AWS_MOBILEANALYTICS_HEADERS})
      source_group("Header Files\\aws\\mobileanalytics\\model" FILES ${AWS_MOBILEANALYTICS_MODEL_HEADERS})
      source_group("Source Files" FILES ${AWS_MOBILEANALYTICS_SOURCE})
      source_group("Source Files\\model" FILES ${AWS_MOBILEANALYTICS_MODEL_SOURCE})
    endif(MSVC)
  endif()

  set(MOBILEANALYTICS_INCLUDES
    "${CMAKE_CURRENT_SOURCE_DIR}/include/"
    "${CORE_DIR}/include/"
  )

  include_directories(${MOBILEANALYTICS_INCLUDES})

  if(USE_WINDOWS_DLL_SEMANTICS AND BUILD_SHARED_LIBS)
    add_definitions("-DAWS_MOBILEANALYTICS_EXPORTS")
  endif()

  add_library(aws-cpp-sdk-mobileanalytics ${LIBTYPE} ${MOBILEANALYTICS_SRC})

  target_include_directories(aws-cpp-sdk-mobileanalytics PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>)
  
  target_link_libraries(aws-cpp-sdk-mobileanalytics aws-cpp-sdk-core)

  if(SIMPLE_INSTALL)
      install (TARGETS aws-cpp-sdk-mobileanalytics
               ARCHIVE DESTINATION ${ARCHIVE_DIRECTORY}
               LIBRARY DESTINATION lib
               RUNTIME DESTINATION bin)
  else()
    if(PLATFORM_CUSTOM)
      install_custom_library(aws-cpp-sdk-mobileanalytics)
    else()
      install (TARGETS aws-cpp-sdk-mobileanalytics
               ARCHIVE DESTINATION ${ARCHIVE_DIRECTORY}/${SDK_INSTALL_BINARY_PREFIX}/${PLATFORM_INSTALL_QUALIFIER}/\${CMAKE_INSTALL_CONFIG_NAME}
               LIBRARY DESTINATION lib/${SDK_INSTALL_BINARY_PREFIX}/${PLATFORM_INSTALL_QUALIFIER}/\${CMAKE_INSTALL_CONFIG_NAME}
               RUNTIME DESTINATION bin/${SDK_INSTALL_BINARY_PREFIX}/${PLATFORM_INSTALL_QUALIFIER}/\${CMAKE_INSTALL_CONFIG_NAME})
    endif()
  endif()

  install (FILES ${AWS_MOBILEANALYTICS_HEADERS} DESTINATION include/aws/mobileanalytics)
  install (FILES ${AWS_MOBILEANALYTICS_MODEL_HEADERS} DESTINATION include/aws/mobileanalytics/model)

  if(MSVC)
    install (FILES nuget/aws-cpp-sdk-mobileanalytics.autopkg DESTINATION nuget)
  endif()



