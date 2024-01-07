cmake_minimum_required (VERSION 2.8)
project(ACMMP LANGUAGES CUDA CXX)

find_package(CUDAToolkit REQUIRED)
find_package(OpenCV REQUIRED)


include_directories(${OpenCV_INCLUDE_DIRS})
include_directories(.)

set(CUDA_NVCC_FLAGS ${CUDA_NVCC_FLAGS};-O3 --use_fast_math --maxrregcount=128 --ptxas-options=-v -std=c++11 --compiler-options -Wall -gencode arch=compute_60,code=sm_60)
if(CMAKE_COMPILER_IS_GNUCXX)
    add_definitions(-std=c++11)
    add_definitions(-pthread)
    add_definitions(-Wall)
    add_definitions(-Wextra)
    add_definitions(-pedantic)
    add_definitions(-Wno-unused-function)
    add_definitions(-Wno-switch)
    set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_C_FLAGS_RELEASE} -O3 -ffast-math -march=native") # extend release-profile with fast-math
endif()
find_package(OpenMP)
if (OPENMP_FOUND)
    set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
endif()


add_executable(
    ${PROJECT_NAME}
    main.h
    ACMMP.h
    ACMMP.cpp
    ACMMP.cu
    main.cpp
)


set_target_properties(${PROJECT_NAME} PROPERTIES
        CUDA_ARCHITECTURES native
)

# For linking ...
# Specify target & libraries to link it with
target_link_libraries(${PROJECT_NAME}
    PRIVATE
    CUDA::cudart
    ${OpenCV_LIBS}
    )
