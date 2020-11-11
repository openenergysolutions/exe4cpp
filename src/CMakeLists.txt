set(exe4cpp_public_headers
    ./exe4cpp/IExecutor.h
    ./exe4cpp/ISteadyTimeSource.h
    ./exe4cpp/ITimer.h
    ./exe4cpp/MockExecutor.h
    ./exe4cpp/Timer.h
    ./exe4cpp/Typedefs.h
)

set(exe4cpp_asio_public_headers
    ./exe4cpp/asio/AsioExecutor.h
    ./exe4cpp/asio/AsioSystemTimer.h
    ./exe4cpp/asio/AsioTimer.h
    ./exe4cpp/asio/BasicExecutor.h
    ./exe4cpp/asio/StrandExecutor.h
    ./exe4cpp/asio/ThreadPool.h
)

add_library(exe4cpp INTERFACE)
#target_sources(exe4cpp INTERFACE ${exe4cpp_public_headers})
target_compile_features(exe4cpp INTERFACE cxx_std_14)
target_include_directories(exe4cpp INTERFACE 
    "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>"
    "$<INSTALL_INTERFACE:include>")

if(TARGET asio)
    find_package(Threads)
    target_link_libraries(exe4cpp INTERFACE asio Threads::Threads)
    #target_sources(exe4cpp INTERFACE ${exe4cpp_asio_public_headers})
endif()

include(CMakePackageConfigHelpers)
write_basic_package_version_file(
    "${PROJECT_BINARY_DIR}/exe4cppConfigVersion.cmake"
    VERSION 1.0
    COMPATIBILITY AnyNewerVersion
)
install(TARGETS exe4cpp
    EXPORT exe4cppTargets
    LIBRARY DESTINATION lib COMPONENT Runtime
    ARCHIVE DESTINATION lib COMPONENT Development
    RUNTIME DESTINATION bin COMPONENT Runtime
    PUBLIC_HEADER DESTINATION include COMPONENT Development
    BUNDLE DESTINATION bin COMPONENT Runtime
    )
include(CMakePackageConfigHelpers)
configure_package_config_file(
    "${PROJECT_SOURCE_DIR}/cmake/exe4cppConfig.cmake.in"
    "${PROJECT_BINARY_DIR}/exe4cppConfig.cmake"
    INSTALL_DESTINATION lib/cmake/exe4cpp
)
install(EXPORT exe4cppTargets DESTINATION lib/cmake/exe4cpp)
install(FILES "${PROJECT_BINARY_DIR}/exe4cppConfigVersion.cmake"
              "${PROJECT_BINARY_DIR}/exe4cppConfig.cmake"
        DESTINATION lib/cmake/exe4cpp)
install(FILES ${exe4cpp_public_headers} DESTINATION include/exe4cpp)
install(DIRECTORY ./exe4cpp/asio DESTINATION include/exe4cpp)
