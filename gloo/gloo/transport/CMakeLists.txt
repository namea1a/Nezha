set(GLOO_TRANSPORT_SRCS
  "${CMAKE_CURRENT_SOURCE_DIR}/address.cc"
  "${CMAKE_CURRENT_SOURCE_DIR}/buffer.cc"
  "${CMAKE_CURRENT_SOURCE_DIR}/context.cc"
  "${CMAKE_CURRENT_SOURCE_DIR}/device.cc"
  "${CMAKE_CURRENT_SOURCE_DIR}/pair.cc"
  "${CMAKE_CURRENT_SOURCE_DIR}/unbound_buffer.cc"
  )

set(GLOO_TRANSPORT_HDRS
  "${CMAKE_CURRENT_SOURCE_DIR}/address.h"
  "${CMAKE_CURRENT_SOURCE_DIR}/buffer.h"
  "${CMAKE_CURRENT_SOURCE_DIR}/context.h"
  "${CMAKE_CURRENT_SOURCE_DIR}/device.h"
  "${CMAKE_CURRENT_SOURCE_DIR}/pair.h"
  "${CMAKE_CURRENT_SOURCE_DIR}/unbound_buffer.h"
  )

if(GLOO_HAVE_TRANSPORT_TCP)
  add_subdirectory(tcp)
endif()

if(GLOO_HAVE_TRANSPORT_IBVERBS)
  add_subdirectory(ibverbs)
endif()

if(GLOO_HAVE_TRANSPORT_UV)
  add_subdirectory(uv)
endif()

if(GLOO_HAVE_TRANSPORT_GLEX)
  add_subdirectory(glex)
endif()

if(GLOO_HAVE_TRANSPORT_GLEX_RDMA_T)
  add_subdirectory(glex_rdma_t)
endif()

# if(GLOO_HAVE_TRANSPORT_GLEX_RDMA_S)
#   add_subdirectory(glex_rdma_t)
# endif()

list(APPEND GLOO_SRCS ${GLOO_TRANSPORT_SRCS})
list(APPEND GLOO_HDRS ${GLOO_TRANSPORT_HDRS})
set(GLOO_SRCS ${GLOO_SRCS} PARENT_SCOPE)
set(GLOO_HDRS ${GLOO_HDRS} PARENT_SCOPE)
