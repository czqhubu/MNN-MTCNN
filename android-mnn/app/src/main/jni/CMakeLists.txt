cmake_minimum_required(VERSION 3.4.1)

#include头文件目录
include_directories(src/../includes/)
include_directories(src/../includes/MNN)
set (CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fopenmp")
set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fopenmp")
#source directory源文件目录
file(GLOB MNN_SRC facedetect_jni.cpp
        face_detect.cpp cJSON.c revertMNNModel.cpp)
set(MNN_COMPILE_CODE ${MNN_SRC})


#添加ncnn库
add_library(libMNN SHARED IMPORTED )
set_target_properties(libMNN
        PROPERTIES IMPORTED_LOCATION
        ${PROJECT_SOURCE_DIR}/libs/${ANDROID_ABI}/libMNN.so )

add_library( opencv_java SHARED IMPORTED )
set_target_properties(
        opencv_java
        PROPERTIES IMPORTED_LOCATION
        ${PROJECT_SOURCE_DIR}/libs/${ANDROID_ABI}/libopencv_java3.so
)

#编译为动态库
add_library(facedetect SHARED ${MNN_COMPILE_CODE})

#添加工程所依赖的库
find_library(  log-lib log )
find_library( jnigraphics-lib jnigraphics )
add_definitions(-DMNN_USE_LOGCAT)
target_link_libraries(  facedetect libMNN opencv_java jnigraphics z ${log-lib} ${jnigraphics-lib})
