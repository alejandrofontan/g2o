CMAKE_MINIMUM_REQUIRED(VERSION 3.16)
SET(CMAKE_LEGACY_CYGWIN_WIN32 0)
set(G2O_VERSION 1.0.0)

PROJECT(g2o)

SET(g2o_C_FLAGS)
SET(g2o_CXX_FLAGS)

set(CMAKE_BUILD_TYPE Release)

#set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -O3 -march=native -W")
#set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -O3 -march=native -W")
set(CMAKE_CXX_STANDARD 17)

SET(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -O3 -march=native") 
SET(CMAKE_C_FLAGS_RELEASE "${CMAKE_C_FLAGS_RELEASE} -O3 -march=native") 
set(CMAKE_CXX_STANDARD_RELEASE 17)

SET(g2o_C_FLAGS "${g2o_C_FLAGS} -Wall -W")
SET(g2o_CXX_FLAGS "${g2o_CXX_FLAGS} -Wall -W")

SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${g2o_CXX_FLAGS}")
SET(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${g2o_C_FLAGS}")

if(MSVC90)
  add_definitions(-DEIGEN_DONT_ALIGN_STATICALLY=1)
  message(STATUS "Disabling memory alignment for MSVC8")
endif(MSVC90)

IF(UNIX)
  ADD_DEFINITIONS(-DUNIX)
  MESSAGE(STATUS "Compiling on Unix")
ENDIF(UNIX)

FIND_PACKAGE(OpenMP)
SET(G2O_USE_OPENMP OFF CACHE BOOL "Build g2o with OpenMP support (EXPERIMENTAL)")
IF(OPENMP_FOUND AND G2O_USE_OPENMP)
  SET (G2O_OPENMP 1)
  SET(g2o_C_FLAGS "${g2o_C_FLAGS} ${OpenMP_C_FLAGS}")
  SET(g2o_CXX_FLAGS "${g2o_CXX_FLAGS} -DEIGEN_DONT_PARALLELIZE ${OpenMP_CXX_FLAGS}")
  MESSAGE(STATUS "Compiling with OpenMP support")
ENDIF(OPENMP_FOUND AND G2O_USE_OPENMP)

find_package(Eigen3  REQUIRED NO_MODULE)

configure_file(config.h.in "${PROJECT_SOURCE_DIR}/g2o/config.h")

set(HDRS_TYPES_G2O
	g2o/types/types_sba.h
	g2o/types/types_six_dof_expmap.h
	g2o/types/types_seven_dof_expmap.h
	g2o/types/se3quat.h
	g2o/types/se3_ops.h
	g2o/types/se3_ops.hpp
)
set(SRCS_TYPES_G2O
	g2o/types/types_sba.cpp
	g2o/types/types_six_dof_expmap.cpp
	g2o/types/types_seven_dof_expmap.cpp
)
  
set(HDRS_CORE_G2O
	g2o/core/base_edge.h
	g2o/core/base_binary_edge.h
	g2o/core/base_binary_edge.hpp
	g2o/core/hyper_graph_action.h
	g2o/core/base_multi_edge.h           
	g2o/core/base_multi_edge.hpp         
	g2o/core/hyper_graph.h
	g2o/core/base_unary_edge.h          
	g2o/core/linear_solver.h
	g2o/core/base_unary_edge.hpp         
	g2o/core/base_vertex.h               
	g2o/core/marginal_covariance_cholesky.h
	g2o/core/base_vertex.hpp             
	g2o/core/matrix_structure.h
	g2o/core/batch_stats.h               
	g2o/core/openmp_mutex.h
	g2o/core/block_solver.h              
	g2o/core/block_solver.hpp            
	g2o/core/parameter.h                 
	g2o/core/cache.h
	g2o/core/optimizable_graph.h         
	g2o/core/solver.h
	g2o/core/creators.h                 
	g2o/core/optimization_algorithm_factory.h
	g2o/core/estimate_propagator.h       
	g2o/core/optimization_algorithm_property.h
	g2o/core/factory.h                   
	g2o/core/sparse_block_matrix.h
	g2o/core/sparse_block_matrix.hpp
	g2o/core/sparse_optimizer.h
	g2o/core/hyper_dijkstra.h
	g2o/core/parameter_container.h
	g2o/core/optimization_algorithm.h
	g2o/core/optimization_algorithm_with_hessian.h
	g2o/core/optimization_algorithm_levenberg.h
	g2o/core/jacobian_workspace.h
	g2o/core/robust_kernel.h
	g2o/core/robust_kernel_factory.h
	g2o/core/robust_kernel_impl.h
)

set(SRCS_CORE_G2O
	g2o/core/hyper_graph_action.cpp        
	g2o/core/hyper_graph.cpp    
	g2o/core/marginal_covariance_cholesky.cpp       
	g2o/core/matrix_structure.cpp
	g2o/core/batch_stats.cpp                    
	g2o/core/parameter.cpp                     
	g2o/core/cache.cpp                   
	g2o/core/optimizable_graph.cpp          
	g2o/core/solver.cpp                             
	g2o/core/optimization_algorithm_factory.cpp
	g2o/core/estimate_propagator.cpp        
	g2o/core/factory.cpp                 
	g2o/core/sparse_optimizer.cpp  
	g2o/core/hyper_dijkstra.cpp 
	g2o/core/parameter_container.cpp     
	g2o/core/optimization_algorithm.cpp 
	g2o/core/optimization_algorithm_with_hessian.cpp 
	g2o/core/optimization_algorithm_levenberg.cpp 
	g2o/core/jacobian_workspace.cpp 
	g2o/core/robust_kernel.cpp 
	g2o/core/robust_kernel_factory.cpp
	g2o/core/robust_kernel_impl.cpp 

)
  
set(HDRS_STUFF_G2O
	g2o/stuff/string_tools.h
	g2o/stuff/color_macros.h 
	g2o/stuff/macros.h
	g2o/stuff/misc.h
	g2o/stuff/timeutil.h
	g2o/stuff/os_specific.h
	g2o/stuff/property.h   

)
set(SRCS_STUFF_G2O
	g2o/stuff/timeutil.cpp
	g2o/stuff/os_specific.c    
	g2o/stuff/string_tools.cpp
	g2o/stuff/property.cpp       
)

# Include the subdirectories
add_library(${PROJECT_NAME} SHARED ${SRCS_TYPES_G2O} ${SRCS_CORE_G2O} ${SRCS_STUFF_G2O})

# Set up the top-level include directories
target_include_directories(${PROJECT_NAME} PUBLIC
	g2o/
	g2o/core
	g2o/types
	g2o/stuff
)

target_link_libraries(${PROJECT_NAME}
        Eigen3::Eigen 
)


configure_file(${PROJECT_SOURCE_DIR}/cmake_modules/g2oConfig.cmake.in
  "${PROJECT_BINARY_DIR}/g2oConfig.cmake" @ONLY)
  
install(FILES "${PROJECT_BINARY_DIR}/g2oConfig.cmake" 
  DESTINATION ${CMAKE_INSTALL_PREFIX}/lib/cmake/${PROJECT_NAME}/)

# Specify installation rules
install(FILES "${PROJECT_SOURCE_DIR}/g2o/config.h" DESTINATION ${CMAKE_INSTALL_PREFIX}/include/g2o)


install(TARGETS ${PROJECT_NAME}
        RUNTIME DESTINATION  ${CMAKE_INSTALL_PREFIX}/bin
        LIBRARY DESTINATION  ${CMAKE_INSTALL_PREFIX}/lib
        ARCHIVE DESTINATION  ${CMAKE_INSTALL_PREFIX}/lib)

install(DIRECTORY
	g2o/
	g2o/core 
	g2o/types 
	g2o/stuff
	
        DESTINATION  ${CMAKE_INSTALL_PREFIX}/include/g2o
        FILES_MATCHING PATTERN "*.h" PATTERN "*.hpp")
