cc_library(
  scale_node
  SRCS scale_node.cc
  DEPS global_utils phi phi_api grad_node_info)

if(NOT (NOT WITH_PYTHON AND ON_INFER))
  cc_library(
    final_dygraph_node
    SRCS nodes.cc
    DEPS ${eager_deps} ${eager_manual_nodes})
  add_dependencies(final_dygraph_node eager_final_state_codegen)
endif()
