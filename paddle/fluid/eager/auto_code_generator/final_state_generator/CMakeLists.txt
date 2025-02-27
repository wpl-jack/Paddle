set(api_yaml_path
    "${PADDLE_SOURCE_DIR}/paddle/phi/api/yaml/api.yaml,${PADDLE_SOURCE_DIR}/paddle/phi/api/yaml/legacy_api.yaml,${PADDLE_SOURCE_DIR}/paddle/phi/api/yaml/sparse_api.yaml"
)
set(backward_yaml_path
    "${PADDLE_SOURCE_DIR}/paddle/phi/api/yaml/backward.yaml,${PADDLE_SOURCE_DIR}/paddle/phi/api/yaml/legacy_backward.yaml,${PADDLE_SOURCE_DIR}/paddle/phi/api/yaml/sparse_bw_api.yaml"
)
set(tmp_forwards_cc_path
    "${PADDLE_SOURCE_DIR}/paddle/fluid/eager/api/generated/eager_generated/forwards/tmp_dygraph_functions.cc"
)
set(tmp_forwards_h_path
    "${PADDLE_SOURCE_DIR}/paddle/fluid/eager/api/generated/eager_generated/forwards/tmp_dygraph_functions.h"
)
set(tmp_nodes_cc_path
    "${PADDLE_SOURCE_DIR}/paddle/fluid/eager/api/generated/eager_generated/backwards/tmp_nodes.cc"
)
set(tmp_nodes_h_path
    "${PADDLE_SOURCE_DIR}/paddle/fluid/eager/api/generated/eager_generated/backwards/tmp_nodes.h"
)
set(forwards_cc_path
    "${PADDLE_SOURCE_DIR}/paddle/fluid/eager/api/generated/eager_generated/forwards/dygraph_functions.cc"
)
set(forwards_h_path
    "${PADDLE_SOURCE_DIR}/paddle/fluid/eager/api/generated/eager_generated/forwards/dygraph_functions.h"
)
set(nodes_cc_path
    "${PADDLE_SOURCE_DIR}/paddle/fluid/eager/api/generated/eager_generated/backwards/nodes.cc"
)
set(nodes_h_path
    "${PADDLE_SOURCE_DIR}/paddle/fluid/eager/api/generated/eager_generated/backwards/nodes.h"
)
# StringTensor only needs forward api
set(fwd_api_yaml_path
    "${PADDLE_SOURCE_DIR}/paddle/phi/api/yaml/strings_api.yaml")

message("Final State Eager CodeGen")
add_custom_target(
  eager_final_state_codegen
  COMMAND
    "${PYTHON_EXECUTABLE}"
    "${PADDLE_SOURCE_DIR}/paddle/fluid/eager/auto_code_generator/final_state_generator/eager_gen.py"
    "--api_yaml_path=${api_yaml_path}"
    "--backward_yaml_path=${backward_yaml_path}"
    "--forwards_cc_path=${tmp_forwards_cc_path}"
    "--forwards_h_path=${tmp_forwards_h_path}"
    "--nodes_cc_path=${tmp_nodes_cc_path}" "--nodes_h_path=${tmp_nodes_h_path}"
  COMMAND ${CMAKE_COMMAND} -E copy_if_different ${tmp_forwards_cc_path}
          ${forwards_cc_path}
  COMMAND ${CMAKE_COMMAND} -E copy_if_different ${tmp_forwards_h_path}
          ${forwards_h_path}
  COMMAND ${CMAKE_COMMAND} -E copy_if_different ${tmp_nodes_cc_path}
          ${nodes_cc_path}
  COMMAND ${CMAKE_COMMAND} -E copy_if_different ${tmp_nodes_h_path}
          ${nodes_h_path}
  VERBATIM)

set(tmp_python_c_output_path
    "${PADDLE_SOURCE_DIR}/paddle/fluid/pybind/eager_final_state_op_function.cc.tmp"
)
set(python_c_output_path
    "${PADDLE_SOURCE_DIR}/paddle/fluid/pybind/eager_final_state_op_function.cc")

add_custom_target(
  eager_final_state_python_c_codegen
  COMMAND
    "${PYTHON_EXECUTABLE}"
    "${PADDLE_SOURCE_DIR}/paddle/fluid/eager/auto_code_generator/final_state_generator/python_c_gen.py"
    "--api_yaml_path=${api_yaml_path},${fwd_api_yaml_path}"
    "--output_path=${tmp_python_c_output_path}"
  COMMAND ${CMAKE_COMMAND} -E copy_if_different ${tmp_python_c_output_path}
          ${python_c_output_path}
  VERBATIM)
