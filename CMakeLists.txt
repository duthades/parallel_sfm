#set(the_description "SFM algorithms")


### LIBMV LIGHT EXTERNAL DEPENDENCIES ###
#OpenCV
find_package( OpenCV REQUIRED )
include_directories( ${OpenCV_INCLUDE_DIRS} )

#Ceres
find_package(Ceres REQUIRED)
include_directories( ${CERES_INCLUDE_DIRS} )

#Eigen
find_package(Eigen3 REQUIRED)
include_directories(${EIGEN3_INCLUDE_DIR})

#OpenMP
find_package(OpenMP)
if(OPENMP_FOUND)
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS}${OpenMP_C_FLAGS}")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS}${OpenMP_CXX_FLAGS}")
endif()


#find_package(Ceres QUIET)
set(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR})
if(NOT DEFINED GFLAGS_LIBRARIES)
  set(GFLAGS_LIBRARIES "gflags")
endif()

if(NOT DEFINED GLOG_LIBRARIES)
  set(GLOG_LIBRARIES "glog")
endif()

if(NOT DEFINED SFM_DEPS_OK)

  set(_fname "${CMAKE_CURRENT_BINARY_DIR}/test_sfm_deps.cpp")
  file(WRITE "${_fname}" "#include <glog/logging.h>\n#include <gflags/gflags.h>\nint main() { (void)(0); return 0; }\n")
  try_compile(SFM_DEPS_OK "${CMAKE_CURRENT_BINARY_DIR}" "${_fname}"
      CMAKE_FLAGS "-DINCLUDE_DIRECTORIES:STRING=${GLOG_INCLUDE_DIRS};${GFLAGS_INCLUDE_DIRS}"
      LINK_LIBRARIES ${GLOG_LIBRARIES} ${GFLAGS_LIBRARIES}
      OUTPUT_VARIABLE OUTPUT
  )
  file(REMOVE "${_fname}")
  message(STATUS "Checking SFM deps... ${SFM_DEPS_OK}")
endif()

if(NOT HAVE_EIGEN OR NOT SFM_DEPS_OK)
  set(DISABLE_MSG "Module opencv_sfm disabled because the following dependencies are not found:")
  if(NOT HAVE_EIGEN)
    set(DISABLE_MSG "${DISABLE_MSG} Eigen")
  endif()
  if(NOT SFM_DEPS_OK)
    set(DISABLE_MSG "${DISABLE_MSG} Glog/Gflags")
  endif()
  message(STATUS ${DISABLE_MSG})
endif()


### LIBMV LIGHT DEFINITIONS ###

set(LIBMV_LIGHT_INCLUDES
  ${CMAKE_MODULE_PATH}/src/libmv_light
  "include/opencv"
  "${GLOG_INCLUDE_DIRS}"
  "${GFLAGS_INCLUDE_DIRS}"
)

set(LIBMV_LIGHT_LIBS
  correspondence
  multiview
  numeric
  ${GLOG_LIBRARIES}
  ${GFLAGS_LIBRARIES}
)

if(Ceres_FOUND)
  add_definitions("-DCERES_FOUND=1")
  list(APPEND LIBMV_LIGHT_LIBS simple_pipeline)
else()
  add_definitions("-DCERES_FOUND=0")
  message(STATUS "CERES support is disabled. Ceres Solver for reconstruction API is required.")
endif()

### DEFINE OPENCV SFM MODULE DEPENDENCIES ###

### CREATE OPENCV SFM MODULE ###

if(UNIX)
  if(CMAKE_COMPILER_IS_GNUCXX OR CV_ICC)
      set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fPIC")
  endif()
endif()

include_directories( ${LIBMV_LIGHT_INCLUDES} )
include_directories(${CMAKE_MODULE_PATH}/include)

# source files
FILE(GLOB OPENCV_SFM_SRC ${CMAKE_MODULE_PATH}/src/*.cpp)

# define the header files (make the headers appear in IDEs.)
FILE(GLOB OPENCV_SFM_HDRS ${CMAKE_MODULE_PATH}/include/opencv2/sfm.hpp ${CMAKE_MODULE_PATH}/include/opencv2/sfm/*.hpp)

set(Main_FILE scene_reconstruction.cpp)
add_executable(foo ${Main_FILE} ${OPENCV_SFM_HDRS} ${OPENCV_SFM_SRC})
#ocv_set_module_sources(HEADERS ${OPENCV_SFM_HDRS} SOURCES ${OPENCV_SFM_SRC})

#ocv_create_module()

# build libmv_light
#if(NOT CMAKE_VERSION VERSION_LESS 2.8.11) # See ocv_target_include_directories() implementation
#  if(TARGET ${the_module})
#    get_target_property(__include_dirs ${the_module} INCLUDE_DIRECTORIES)
#    include_directories(${__include_dirs})
#  endif()
#endif()

#include_directories(${OCV_TARGET_INCLUDE_DIRS_${the_module}})

add_subdirectory(src/libmv_light)

target_link_libraries(foo ${LIBMV_LIGHT_LIBS} ${OpenCV_LIBS} ${EIGEN3_LIBRARIES} ${CERES_LIBRARIES} -D WITH_OPENMP=ON)


