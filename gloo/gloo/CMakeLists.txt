# GLOO_SRCS is the list of source files that we need to build libgloo.
set(GLOO_SRCS)

# GLOO_HDRS is the list of header files that we need to install.
set(GLOO_HDRS)

# Compiled sources in root directory
list(APPEND GLOO_SRCS
  "${CMAKE_CURRENT_SOURCE_DIR}/algorithm.cc"
  "${CMAKE_CURRENT_SOURCE_DIR}/allgather.cc"
  "${CMAKE_CURRENT_SOURCE_DIR}/allgatherv.cc"
  "${CMAKE_CURRENT_SOURCE_DIR}/allreduce.cc"
  "${CMAKE_CURRENT_SOURCE_DIR}/allreduce_local.cc"
  "${CMAKE_CURRENT_SOURCE_DIR}/barrier.cc"
  "${CMAKE_CURRENT_SOURCE_DIR}/broadcast.cc"
  "${CMAKE_CURRENT_SOURCE_DIR}/context.cc"
  "${CMAKE_CURRENT_SOURCE_DIR}/gather.cc"
  "${CMAKE_CURRENT_SOURCE_DIR}/gatherv.cc"
  "${CMAKE_CURRENT_SOURCE_DIR}/reduce.cc"
  "${CMAKE_CURRENT_SOURCE_DIR}/scatter.cc"
  "${CMAKE_CURRENT_SOURCE_DIR}/types.cc"
  )

list(APPEND GLOO_HDRS
  "${CMAKE_CURRENT_SOURCE_DIR}/algorithm.h"
  "${CMAKE_CURRENT_SOURCE_DIR}/allgather.h"
  "${CMAKE_CURRENT_SOURCE_DIR}/allgather_ring.h"
  "${CMAKE_CURRENT_SOURCE_DIR}/allgatherv.h"
  "${CMAKE_CURRENT_SOURCE_DIR}/allreduce.h"
  "${CMAKE_CURRENT_SOURCE_DIR}/allreduce_bcube.h"
  "${CMAKE_CURRENT_SOURCE_DIR}/allreduce_halving_doubling.h"
  "${CMAKE_CURRENT_SOURCE_DIR}/allreduce_local.h"
  "${CMAKE_CURRENT_SOURCE_DIR}/allreduce_ring.h"
  "${CMAKE_CURRENT_SOURCE_DIR}/allreduce_ring_chunked.h"
  "${CMAKE_CURRENT_SOURCE_DIR}/barrier.h"
  "${CMAKE_CURRENT_SOURCE_DIR}/barrier_all_to_all.h"
  "${CMAKE_CURRENT_SOURCE_DIR}/barrier_all_to_one.h"
  "${CMAKE_CURRENT_SOURCE_DIR}/broadcast.h"
  "${CMAKE_CURRENT_SOURCE_DIR}/broadcast_one_to_all.h"
  "${CMAKE_CURRENT_SOURCE_DIR}/context.h"
  "${CMAKE_CURRENT_SOURCE_DIR}/gather.h"
  "${CMAKE_CURRENT_SOURCE_DIR}/math.h"
  "${CMAKE_CURRENT_SOURCE_DIR}/pairwise_exchange.h"
  "${CMAKE_CURRENT_SOURCE_DIR}/reduce.h"
  "${CMAKE_CURRENT_SOURCE_DIR}/reduce_scatter.h"
  "${CMAKE_CURRENT_SOURCE_DIR}/scatter.h"
  "${CMAKE_CURRENT_SOURCE_DIR}/types.h"
  )

if(USE_CUDA)
  file(GLOB GLOO_CUDA_SRCS
    "${CMAKE_CURRENT_SOURCE_DIR}/cuda*.cc"
    "${CMAKE_CURRENT_SOURCE_DIR}/cuda*.cu"
    )

  file(GLOB GLOO_CUDA_HDRS
    "${CMAKE_CURRENT_SOURCE_DIR}/cuda*.h"
    )
endif()

if(USE_ROCM)
  file(GLOB GLOO_HIP_SRCS
    "${HIPIFY_OUTPUT_DIR}/hip*.cc"
    "${HIPIFY_OUTPUT_DIR}/hip*.hip"
    "${HIPIFY_OUTPUT_DIR}/nccl/nccl.hip"
    )

  file(GLOB GLOO_HIP_HDRS
    "${HIPIFY_OUTPUT_DIR}/hip*.h"
    "${HIPIFY_OUTPUT_DIR}/nccl/nccl.h"
    )
endif()

# Compile TCP transport only on Linux (it uses epoll(2))
if(${CMAKE_SYSTEM_NAME} STREQUAL "Linux")
  set(GLOO_HAVE_TRANSPORT_TCP 1)
else()
  set(GLOO_HAVE_TRANSPORT_TCP 0)
endif()

# Compile ibverbs transport if libibverbs is available
if(USE_IBVERBS)
  set(GLOO_HAVE_TRANSPORT_IBVERBS 1)
else()
  set(GLOO_HAVE_TRANSPORT_IBVERBS 0)
endif()

# Compile uv transport if libuv is available
if(USE_LIBUV)
  set(GLOO_HAVE_TRANSPORT_UV 1)
else()
  set(GLOO_HAVE_TRANSPORT_UV 0)
endif()

# Compile glex transport if glex is available
if(USE_GLEX)
  set(GLOO_HAVE_TRANSPORT_GLEX 1)
else()
  set(GLOO_HAVE_TRANSPORT_GLEX 0)
endif()

# Compile glex_rdma_t transport if glex_rdma_t is available
if(USE_GLEX_RDMA_T)
  set(GLOO_HAVE_TRANSPORT_GLEX_RDMA_T 1)
else()
  set(GLOO_HAVE_TRANSPORT_GLEX_RDMA_T 0)
endif()

if(USE_GLEX_RDMA_S)
  set(GLOO_HAVE_TRANSPORT_GLEX_RDMA_S 1)
else()
  set(GLOO_HAVE_TRANSPORT_GLEX_RDMA_S 0)
endif()

# Compile sharp transport if libsharp & libsharp_coll is available
if(USE_PIPE)
list(APPEND GLOO_SRCS
"${CMAKE_CURRENT_SOURCE_DIR}/pipeallreduce.cc"
"${CMAKE_CURRENT_SOURCE_DIR}/pipeallreduce-a.cc"
"${CMAKE_CURRENT_SOURCE_DIR}/pipeallreduce-s.cc"
)
list(APPEND GLOO_HDRS
"${CMAKE_CURRENT_SOURCE_DIR}/pipeallreduce.h"
"${CMAKE_CURRENT_SOURCE_DIR}/pipeallreduce-a.h"
"${CMAKE_CURRENT_SOURCE_DIR}/pipeallreduce-s.h"
)
set(GLOO_HAVE_TRANSPORT_PIPE 1)
set(USE_SHARP 1)
else()
set(GLOO_HAVE_TRANSPORT_PIPE 0)
endif()

if(USE_SHARP)
  list(APPEND GLOO_SRCS
    "${CMAKE_CURRENT_SOURCE_DIR}/sharp_allreduce.cc"
    "${CMAKE_CURRENT_SOURCE_DIR}/sharp_reduce.cc"
    "${CMAKE_CURRENT_SOURCE_DIR}/sharp_bcast.cc"
    "${CMAKE_CURRENT_SOURCE_DIR}/sharp_allgather.cc"
    "${CMAKE_CURRENT_SOURCE_DIR}/sharp_barrier.cc"
    "${CMAKE_CURRENT_SOURCE_DIR}/pipeallreduce.cc"
    "${CMAKE_CURRENT_SOURCE_DIR}/pipeallreduce-a.cc"
    "${CMAKE_CURRENT_SOURCE_DIR}/pipeallreduce-s.cc"    
  )
  list(APPEND GLOO_HDRS
    "${CMAKE_CURRENT_SOURCE_DIR}/sharp_allreduce.h"
    "${CMAKE_CURRENT_SOURCE_DIR}/sharp_reduce.h"
    "${CMAKE_CURRENT_SOURCE_DIR}/sharp_bcast.h"
    "${CMAKE_CURRENT_SOURCE_DIR}/sharp_allgather.h"
    "${CMAKE_CURRENT_SOURCE_DIR}/sharp_barrier.h"
    "${CMAKE_CURRENT_SOURCE_DIR}/pipeallreduce.h"
    "${CMAKE_CURRENT_SOURCE_DIR}/pipeallreduce-a.h"
    "${CMAKE_CURRENT_SOURCE_DIR}/pipeallreduce-s.h"    
  )
  set(GLOO_HAVE_TRANSPORT_SHARP 1)
else()
  set(GLOO_HAVE_TRANSPORT_SHARP 0)
endif()

add_subdirectory(common)
add_subdirectory(mpi)
if(USE_CUDA AND USE_NCCL)
  add_subdirectory(nccl)
endif()
if(USE_ROCM AND USE_RCCL)
  add_subdirectory(nccl)
endif()
add_subdirectory(rendezvous)
add_subdirectory(transport)

# Depend on pthreads for transport device threads
list(APPEND gloo_DEPENDENCY_LIBS pthread)

# Enable the following to get a list of source files
if(FALSE)
  message(STATUS "Sources: ")
  foreach(tmp ${GLOO_SRCS})
    message(STATUS "  " ${tmp})
  endforeach()
endif()

# Write configuration header.
# Set variables so macros have GLOO_ prefix.
set(GLOO_USE_CUDA ${USE_CUDA})
set(GLOO_USE_NCCL ${USE_NCCL})
set(GLOO_USE_ROCM ${USE_ROCM})
set(GLOO_USE_RCCL ${USE_RCCL})
set(GLOO_USE_REDIS ${USE_REDIS})
set(GLOO_USE_IBVERBS ${USE_IBVERBS})
set(GLOO_USE_MPI ${USE_MPI})
set(GLOO_USE_AVX ${USE_AVX})
set(GLOO_USE_LIBUV ${USE_LIBUV})
set(GLOO_USE_SHARP ${USE_SHARP})
set(GLOO_USE_PIPE ${USE_PIPE})
set(GLOO_USE_GLEX ${USE_GLEX})
set(GLOO_USE_GLEX_RDMA_T ${USE_GLEX_RDMA_T})
configure_file(config.h.in config.h)

add_library(gloo ${GLOO_STATIC_OR_SHARED} ${GLOO_SRCS})
if(USE_CUDA)
  cuda_add_library(gloo_cuda ${GLOO_CUDA_SRCS} ${GLOO_STATIC_OR_SHARED})
  target_link_libraries(gloo_cuda gloo ${gloo_cuda_DEPENDENCY_LIBS})
endif()
if(USE_ROCM)
  gloo_hip_add_library(gloo_hip ${GLOO_HIP_SRCS})
  target_link_libraries(gloo_hip gloo)
endif()
if(USE_LIBUV)
  target_link_libraries(gloo PRIVATE uv_a)
endif()

if(USE_SHARP)
  target_link_libraries(gloo PRIVATE sharp)
  target_link_libraries(gloo PRIVATE sharp_coll)
endif()
if(USE_PIPE)
  target_link_libraries(gloo PRIVATE pipe)
endif()

if(USE_GLEX)
  target_link_libraries(gloo PRIVATE glex)
endif()

if(USE_GLEX_RDMA_T)
  target_link_libraries(gloo PRIVATE glex)
endif()

target_link_libraries(gloo PRIVATE ${gloo_DEPENDENCY_LIBS})

# Add Interface include directories that are relocatable.
target_include_directories(gloo INTERFACE $<INSTALL_INTERFACE:include>)
if(USE_CUDA)
  target_include_directories(gloo_cuda INTERFACE $<INSTALL_INTERFACE:include>)
endif()
if(USE_ROCM)
  target_include_directories(gloo_hip INTERFACE $<INSTALL_INTERFACE:include>)
endif()

# Install if necessary.
# If the Gloo build is included from another project's build, it may
# want to statically link with Gloo and not install any artifacts.
if(GLOO_INSTALL)
  install(TARGETS gloo EXPORT GlooTargets
      DESTINATION ${CMAKE_INSTALL_PREFIX}/lib)
  if(USE_CUDA)
    install(TARGETS gloo_cuda EXPORT GlooTargets
        DESTINATION ${CMAKE_INSTALL_PREFIX}/lib)
  endif()
  if(USE_ROCM)
    install(TARGETS gloo_hip EXPORT GlooTargets
        DESTINATION ${CMAKE_INSTALL_PREFIX}/lib)
  endif()
  install(FILES ${CMAKE_CURRENT_BINARY_DIR}/config.h
    DESTINATION ${CMAKE_INSTALL_PREFIX}/include/gloo)
  foreach(HEADER ${GLOO_HDRS})
    string(REGEX MATCH "(.*)[/\\]" DIR ${HEADER})
    string(REGEX REPLACE "${CMAKE_CURRENT_SOURCE_DIR}" "gloo" DIR ${DIR})
    install(FILES ${HEADER} DESTINATION ${CMAKE_INSTALL_PREFIX}/include/${DIR})
  endforeach()
  if(USE_CUDA)
    foreach(HEADER ${GLOO_CUDA_HDRS})
      string(REGEX MATCH "(.*)[/\\]" DIR ${HEADER})
      string(REGEX REPLACE "${CMAKE_CURRENT_SOURCE_DIR}" "gloo" DIR ${DIR})
      install(FILES ${HEADER} DESTINATION ${CMAKE_INSTALL_PREFIX}/include/${DIR})
    endforeach()
  endif()
  if(USE_ROCM)
    foreach(HEADER ${GLOO_HIP_HDRS})
      string(REGEX MATCH "(.*)[/\\]" DIR ${HEADER})
      string(REGEX REPLACE "${CMAKE_CURRENT_SOURCE_DIR}" "gloo" DIR ${DIR})
      install(FILES ${HEADER} DESTINATION ${CMAKE_INSTALL_PREFIX}/include/${DIR})
    endforeach()
  endif()
endif()

if(BUILD_TEST)
add_subdirectory(test)
endif()

if(BUILD_BENCHMARK)
add_subdirectory(benchmark)
endif()

if(BUILD_EXAMPLES)
  add_subdirectory(examples)
endif()
