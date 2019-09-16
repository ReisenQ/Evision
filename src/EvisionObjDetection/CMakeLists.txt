
# required cmake version
cmake_minimum_required(VERSION 3.13.0)
# packages
enable_language(CUDA) 
# find_package(OpenCV REQUIRED)
# message(STATUS "OpenCV library status:")
# message(STATUS "    version: ${OpenCV_VERSION}")
# message(STATUS "    libraries: ${OpenCV_LIBS}")
# message(STATUS "    include path: ${OpenCV_INCLUDE_DIRS}")


include_directories(${CUDA_INCLUDE_DIRS})
include_directories(${CUDA_TOOLKIT_INCLUDE})
# include_directories(${OpenCV_INCLUDE_DIRS})
include_directories("./include/")
include_directories("../../package/pthread/include/")

find_library(CUDA_BLAS_LIBRARY cublas ${CMAKE_CUDA_IMPLICIT_LINK_DIRECTORIES})
find_library(CUDA_RANDOM_LIBRARY curand ${CMAKE_CUDA_IMPLICIT_LINK_DIRECTORIES})

# set(CUDA_SEPARABLE_COMPILATION ON)
set(CUDA_PROPAGATE_HOST_FLAGS ON)
set(CUDA_BUILD_EMULATION OFF)

set(CUDA_NVCC_FLAGS ${CUDA_NVCC_FLAGS};-arch=sm_52;-O3;-G;-g;-std=c++11)

link_directories("../../package/pthread/lib/x64/")
add_compile_options(-Xcompiler "/wd4819 ")
# add_definitions(-D OPENCV)
add_definitions(-D _MBCS)
add_definitions(-D LIB_EXPORTS)
add_definitions(-D _TIMESPEC_DEFINED)
add_definitions(-D _SCL_SECURE_NO_WARNINGS)
add_definitions(-D _CRT_SECURE_NO_WARNINGS)
add_definitions(-D GPU)
add_definitions(-D WIN32)
add_definitions(-D NDEBUG)
add_definitions(-D _CONSOLE)
add_definitions(-D _LIB)

# add_definitions(-D __cplusplus)
# add_definitions(-D __CUDACC__)

FILE(GLOB HEAD_FILES "./*.h") 
FILE(GLOB SRC_FILES "./*.c") 
FILE(GLOB CU_FILES "./*.cu")

source_group("Header Files" FILES ${HEAD_FILES})
source_group("Source Files" FILES ${SRC_FILES})
source_group("CU Files" FILES ${CU_FILES})

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY_DEBUG ${PROJECT_SOURCE_DIR}/../install/Debug/lib)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY_RELEASE ${PROJECT_SOURCE_DIR}/../install/Release/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_DEBUG ${PROJECT_SOURCE_DIR}/../install/Debug/bin)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_RELEASE ${PROJECT_SOURCE_DIR}/../install/Release/bin)
set(CMAKE_DEBUG_POSTFIX "_d")
set_target_properties(${TARGET_NAME} PROPERTIES DEBUG_POSTFIX "_d")

ADD_LIBRARY(EvisionObjDetection SHARED ${HEAD_FILES} ${SRC_FILES} ${CU_FILES})
target_link_libraries(EvisionObjDetection ../../package/pthread/lib/x64/pthreadVC2)
target_link_libraries(EvisionObjDetection ${CUDA_BLAS_LIBRARY} ${CUDA_RANDOM_LIBRARY} ${CUDA_CUDA_LIBRARY} ${CUDA_cublas_LIBRARY} ${CUDA_curand_LIBRARY} ${CUDA_CUDART_LIBRARY})