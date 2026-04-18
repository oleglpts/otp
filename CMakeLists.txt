cmake_minimum_required(VERSION 3.28.3)
project(otp)

set(CMAKE_CXX_STANDARD 11)

add_library(qrcodegen SHARED qrcodegen.cpp)
target_include_directories(qrcodegen PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})

add_library(${PROJECT_NAME} SHARED cotp.cpp
        otp_factory.cpp
        otp_uri.cpp
        qr_code.cpp)
target_include_directories(${PROJECT_NAME} PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})

add_executable(test cotp_test.cpp)
target_include_directories(test PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})
target_link_libraries(test ${PROJECT_NAME} qrcodegen crypto)

add_executable(qrcodegen_demo QrCodeGeneratorDemo.cpp)
target_link_libraries(qrcodegen_demo ${PROJECT_NAME} qrcodegen crypto)
