cmake_minimum_required(VERSION 2.8.12)
project(aws-cpp-sdk-ecs)

  file(GLOB AWS_ECS_HEADERS
    "include/aws/ecs/*.h"
  )

  file(GLOB AWS_ECS_MODEL_HEADERS
    "include/aws/ecs/model/*.h"
  )

  file(GLOB AWS_ECS_SOURCE
    "source/*.cpp"
  )

  file(GLOB AWS_ECS_MODEL_SOURCE
    "source/model/*.cpp"
  )

  file(GLOB ECS_UNIFIED_HEADERS
    ${AWS_ECS_HEADERS}
    ${AWS_ECS_MODEL_HEADERS}
  )

  file(GLOB ECS_UNITY_SRC
    ${AWS_ECS_SOURCE}
    ${AWS_ECS_MODEL_SOURCE}
  )

  if(ENABLE_UNITY_BUILD)
    enable_unity_build("ECS" ECS_UNITY_SRC)
  endif()

  file(GLOB ECS_SRC
    ${ECS_UNIFIED_HEADERS}
    ${ECS_UNITY_SRC}
  )

  if(WIN32)
    #if we are compiling for visual studio, create a sane directory tree.
    if(MSVC)
      source_group("Header Files\\aws\\ecs" FILES ${AWS_ECS_HEADERS})
      source_group("Header Files\\aws\\ecs\\model" FILES ${AWS_ECS_MODEL_HEADERS})
      source_group("Source Files" FILES ${AWS_ECS_SOURCE})
      source_group("Source Files\\model" FILES ${AWS_ECS_MODEL_SOURCE})
    endif(MSVC)
  endif()

  set(ECS_INCLUDES
    "${CMAKE_CURRENT_SOURCE_DIR}/include/"
    "${CORE_DIR}/include/"
  )

  include_directories(${ECS_INCLUDES})

  if(USE_WINDOWS_DLL_SEMANTICS AND BUILD_SHARED_LIBS)
    add_definitions("-DAWS_ECS_EXPORTS")
  endif()

  add_library(aws-cpp-sdk-ecs ${LIBTYPE} ${ECS_SRC})

  target_include_directories(aws-cpp-sdk-ecs PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>)
  
  target_link_libraries(aws-cpp-sdk-ecs aws-cpp-sdk-core)

  if(SIMPLE_INSTALL)
      install (TARGETS aws-cpp-sdk-ecs
               ARCHIVE DESTINATION ${ARCHIVE_DIRECTORY}
               LIBRARY DESTINATION lib
               RUNTIME DESTINATION bin)
  else()
    if(PLATFORM_CUSTOM)
      install_custom_library(aws-cpp-sdk-ecs)
    else()
      install (TARGETS aws-cpp-sdk-ecs
               ARCHIVE DESTINATION ${ARCHIVE_DIRECTORY}/${SDK_INSTALL_BINARY_PREFIX}/${PLATFORM_INSTALL_QUALIFIER}/\${CMAKE_INSTALL_CONFIG_NAME}
               LIBRARY DESTINATION lib/${SDK_INSTALL_BINARY_PREFIX}/${PLATFORM_INSTALL_QUALIFIER}/\${CMAKE_INSTALL_CONFIG_NAME}
               RUNTIME DESTINATION bin/${SDK_INSTALL_BINARY_PREFIX}/${PLATFORM_INSTALL_QUALIFIER}/\${CMAKE_INSTALL_CONFIG_NAME})
    endif()
  endif()

  install (FILES ${AWS_ECS_HEADERS} DESTINATION include/aws/ecs)
  install (FILES ${AWS_ECS_MODEL_HEADERS} DESTINATION include/aws/ecs/model)

  if(MSVC)
    install (FILES nuget/aws-cpp-sdk-ecs.autopkg DESTINATION nuget)
  endif()



