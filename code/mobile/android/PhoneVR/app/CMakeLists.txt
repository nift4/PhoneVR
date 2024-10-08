cmake_minimum_required(VERSION 3.4.3)

#use rtti, exceptions for asio library ## -std=c++17
#set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -g -frtti -fexceptions -Wc++11-narrowing -Wreorder")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++17 -g")
set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS} -O3 -std=c++17 -g")
set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS} -O3 -std=c++17 -g")

set(ANDROID_STL c++_static)
set(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY BOTH)

file(GLOB_RECURSE LIB_SRC
    ../../../../common/libs/ifaddrs/*.c
    ../../../../common/libs/json/single_include/*.hpp
)

add_library(native-lib-alvr SHARED
    src/main/cpp/alvr_main.cpp
    ${LIB_SRC}
)

set(libs_dir ${CMAKE_CURRENT_SOURCE_DIR}/libraries)
set(alvr_build_dir ${CMAKE_CURRENT_SOURCE_DIR}/../ALVR/build/alvr_client_core)

target_include_directories(native-lib-alvr
    PUBLIC ../../../../common/libs/asio/asio/include
    PUBLIC ../../../../common/libs/eigen/Eigen
    PUBLIC ../../../../common/libs/ifaddrs
    PUBLIC ../../../../common/libs/json/single_include/

    # ALVR Headers
    PUBLIC ${alvr_build_dir}
    PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/../cardboard
)

target_link_libraries(native-lib-alvr
    log android EGL GLESv3 mediandk
    ${alvr_build_dir}/${ANDROID_ABI}/libalvr_client_core.so
    ${libs_dir}/jni/${ANDROID_ABI}/libGfxPluginCardboard.so
)

IF(${HAVEGVR})
add_subdirectory(src/gvr)
ENDIF()
