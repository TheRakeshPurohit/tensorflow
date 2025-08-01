load("@rules_cc//cc:cc_library.bzl", "cc_library")
load("//tensorflow:tensorflow.bzl", "tf_cc_test")
load("//tensorflow/core/platform:build_config.bzl", "tf_proto_library")

package(
    # copybara:uncomment default_applicable_licenses = ["//tensorflow:license"],
    default_visibility = [
        # copybara:uncomment "//learning/brain/tfrt:__subpackages__",
        "//tensorflow/compiler/mlir/tfrt:__subpackages__",
        "//tensorflow/core/tfrt:__subpackages__",
        "//tensorflow/core/tfrt/mlrt:__subpackages__",
    ],
)

tf_proto_library(
    name = "ifrt_config_proto",
    srcs = ["ifrt_config.proto"],
    protodeps = [
        "@local_xla//xla:xla_data_proto",
        "//tensorflow/core/framework:tensor_proto",
    ],
    visibility = ["//visibility:public"],
)

cc_library(
    name = "ifrt_serving_core_selector",
    srcs = ["ifrt_serving_core_selector.cc"],
    hdrs = ["ifrt_serving_core_selector.h"],
    deps = [
        "@com_google_absl//absl/base:core_headers",
        "@com_google_absl//absl/container:flat_hash_map",
        "@com_google_absl//absl/strings",
        "@com_google_absl//absl/synchronization",
        "@local_xla//xla/tsl/framework:serving_device_selector",
    ],
)

tf_cc_test(
    name = "ifrt_serving_core_selector_test",
    srcs = ["ifrt_serving_core_selector_test.cc"],
    tags = ["no_oss"],
    deps = [
        ":ifrt_serving_core_selector",
        "@com_google_absl//absl/strings",
        "@com_google_googletest//:gtest_main",
        "@local_xla//xla/tsl/framework:serving_device_selector",
        "@local_xla//xla/tsl/framework/test_util:mock_serving_device_selector",
    ],
)

cc_library(
    name = "grid",
    srcs = ["grid.cc"],
    hdrs = ["grid.h"],
    deps = [
        "@com_google_absl//absl/log:check",
        "@com_google_absl//absl/strings",
        "@com_google_absl//absl/strings:str_format",
    ],
)

cc_library(
    name = "ifrt_device_utils",
    srcs = ["ifrt_device_utils.cc"],
    hdrs = ["ifrt_device_utils.h"],
    deps = [
        ":grid",
        ":ifrt_config_proto_cc",
        "//tensorflow/compiler/tf2xla:host_compute_metadata_proto_cc",
        "//tensorflow/compiler/tf2xla:xla_helpers",
        "//tensorflow/core:core_cpu_base",
        "//tensorflow/core:protos_all_cc",
        "//tensorflow/core/protobuf/tpu:compile_metadata_proto_cc",
        "@com_google_absl//absl/algorithm:container",
        "@com_google_absl//absl/container:flat_hash_map",
        "@com_google_absl//absl/container:flat_hash_set",
        "@com_google_absl//absl/log",
        "@com_google_absl//absl/log:check",
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/status:statusor",
        "@com_google_absl//absl/strings",
        "@llvm-project//llvm:Support",
        "@llvm-project//mlir:IR",
        "@llvm-project//mlir:Support",
        "@local_xla//xla:xla_data_proto_cc",
        "@local_xla//xla/python/ifrt",
        "@local_xla//xla/python/ifrt:attribute_map",
        "@local_xla//xla/service:computation_placer_hdr",
        "@local_xla//xla/tsl/platform:statusor",
    ],
)

tf_cc_test(
    name = "ifrt_device_utils_test",
    srcs = [
        "ifrt_device_utils_test.cc",
    ],
    tags = ["no_oss"],
    deps = [
        ":ifrt_device_utils",
        "@com_google_absl//absl/status",
        "@com_google_googletest//:gtest_main",
        "@local_xla//xla/python/ifrt",
        "@local_xla//xla/python/ifrt:attribute_map",
        "@local_xla//xla/python/ifrt:mock",
        "@local_xla//xla/service:computation_placer_hdr",
        "@local_xla//xla/tsl/platform:status_matchers",
        "@local_xla//xla/tsl/platform:statusor",
    ],
)

cc_library(
    name = "ifrt_serving_executable",
    srcs = ["ifrt_serving_executable.cc"],
    hdrs = ["ifrt_serving_executable.h"],
    deps = [
        ":ifrt_config_proto_cc",
        ":ifrt_device_utils",
        ":ifrt_loaded_variable_registry",
        ":ifrt_loaded_variable_utils",
        ":ifrt_persistent_compilation_cache",
        ":ifrt_restore_tensor_registry",
        ":ifrt_serving_core_selector",
        ":ifrt_tensor_utils",
        ":sharding_utils",
        ":tf_host_callback",
        "//tensorflow/compiler/mlir/tensorflow",
        "//tensorflow/compiler/mlir/tensorflow:dump_mlir_util",
        "//tensorflow/compiler/mlir/tfrt:export",
        "//tensorflow/compiler/mlir/tfrt/transforms/ifrt:extract_callback",
        "//tensorflow/compiler/mlir/tfrt/transforms/ifrt:ifrt_types",
        "//tensorflow/compiler/mlir/tfrt/transforms/ifrt:tf2hlo",
        "//tensorflow/compiler/tf2xla:common",
        "//tensorflow/compiler/tf2xla:host_compute_metadata_proto_cc",
        "//tensorflow/compiler/tf2xla:xla_helpers",
        "//tensorflow/core:core_cpu_base",
        "//tensorflow/core:framework",
        "//tensorflow/core:protos_all_cc",
        "//tensorflow/core/protobuf/tpu:compile_metadata_proto_cc",
        "@com_google_absl//absl/base:core_headers",
        "@com_google_absl//absl/container:flat_hash_map",
        "@com_google_absl//absl/container:flat_hash_set",
        "@com_google_absl//absl/log",
        "@com_google_absl//absl/log:check",
        "@com_google_absl//absl/memory",
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/status:statusor",
        "@com_google_absl//absl/strings",
        "@com_google_absl//absl/synchronization",
        "@com_google_absl//absl/types:span",
        "@llvm-project//llvm:Support",
        "@llvm-project//mlir:FuncDialect",
        "@llvm-project//mlir:IR",
        "@llvm-project//mlir:Pass",
        "@llvm-project//mlir:Support",
        "@local_tsl//tsl/platform:tstring",
        "@local_tsl//tsl/profiler/lib:traceme",
        "@local_xla//xla:shape_util",
        "@local_xla//xla:xla_data_proto_cc",
        "@local_xla//xla/hlo/ir:hlo",
        "@local_xla//xla/hlo/translate:stablehlo",
        "@local_xla//xla/pjrt:host_callback",
        "@local_xla//xla/pjrt:pjrt_compiler",
        "@local_xla//xla/pjrt:pjrt_executable",
        "@local_xla//xla/python/ifrt",
        "@local_xla//xla/python/ifrt/hlo:hlo_program",
        "@local_xla//xla/python/pjrt_ifrt",
        "@local_xla//xla/service:computation_placer_hdr",
        "@local_xla//xla/service:dump",
        "@local_xla//xla/service/spmd/shardy/sdy_round_trip:pipelines",
        "@local_xla//xla/service/spmd/shardy/stablehlo_round_trip:stablehlo_import",
        "@local_xla//xla/tsl/concurrency:ref_count",
        "@local_xla//xla/tsl/framework:serving_device_selector",
        "@local_xla//xla/tsl/framework/mlir:status_scoped_diagnostic_handler",
        "@local_xla//xla/tsl/platform:env",
        "@local_xla//xla/tsl/platform:errors",
        "@local_xla//xla/tsl/platform:statusor",
        "@shardy//shardy/dialect/sdy/transforms/import:passes",
        "@tf_runtime//:hostcontext",
    ],
)

cc_library(
    name = "ifrt_executable_registry",
    srcs = ["ifrt_executable_registry.cc"],
    hdrs = ["ifrt_executable_registry.h"],
    deps = [
        ":ifrt_serving_executable",
        "@com_google_absl//absl/base:core_headers",
        "@com_google_absl//absl/container:flat_hash_map",
        "@com_google_absl//absl/log",
        "@com_google_absl//absl/log:check",
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/status:statusor",
        "@com_google_absl//absl/strings",
        "@com_google_absl//absl/synchronization",
    ],
)

cc_library(
    name = "ifrt_restore_tensor_registry",
    srcs = ["ifrt_restore_tensor_registry.cc"],
    hdrs = ["ifrt_restore_tensor_registry.h"],
    deps = [
        "//tensorflow/compiler/mlir/tfrt/transforms/ifrt:ifrt_types",
        "//tensorflow/core:framework",
        "//tensorflow/core/framework:tensor",
        "//tensorflow/core/framework:types_proto_cc",
        "@com_google_absl//absl/base:core_headers",
        "@com_google_absl//absl/container:flat_hash_map",
        "@com_google_absl//absl/log",
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/status:statusor",
        "@com_google_absl//absl/strings",
        "@com_google_absl//absl/synchronization",
        "@local_xla//xla/python/ifrt",
    ],
)

cc_library(
    name = "ifrt_persistent_compilation_cache",
    srcs = ["ifrt_persistent_compilation_cache.cc"],
    hdrs = ["ifrt_persistent_compilation_cache.h"],
    deps = [
        "//tensorflow/compiler/mlir/tfrt/transforms/ifrt:ifrt_types",
        "//tensorflow/compiler/mlir/tfrt/transforms/ifrt:tf2hlo",
        "@com_google_absl//absl/functional:any_invocable",
        "@com_google_absl//absl/status:statusor",
        "@com_google_absl//absl/strings:string_view",
        "@com_google_absl//absl/types:span",
        "@llvm-project//mlir:IR",
        "@local_xla//xla/pjrt:pjrt_executable",
        "@local_xla//xla/python/ifrt",
        "@local_xla//xla/python/ifrt/hlo:hlo_program",
        "@local_xla//xla/python/pjrt_ifrt:xla_ifrt",
        "@local_xla//xla/tsl/concurrency:ref_count",
    ],
)

cc_library(
    name = "ifrt_loaded_variable_registry",
    srcs = ["ifrt_loaded_variable_registry.cc"],
    hdrs = ["ifrt_loaded_variable_registry.h"],
    deps = [
        "@com_google_absl//absl/base:core_headers",
        "@com_google_absl//absl/container:flat_hash_map",
        "@com_google_absl//absl/container:flat_hash_set",
        "@com_google_absl//absl/functional:any_invocable",
        "@com_google_absl//absl/log",
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/status:statusor",
        "@com_google_absl//absl/strings",
        "@com_google_absl//absl/synchronization",
        "@local_xla//xla/hlo/ir:hlo",
        "@local_xla//xla/python/ifrt",
        "@local_xla//xla/tsl/concurrency:ref_count",
        "@local_xla//xla/tsl/platform:statusor",
    ],
)

cc_library(
    name = "ifrt_model_restore_context",
    hdrs = ["ifrt_model_restore_context.h"],
    deps = [
        ":checkpoint_loader",
        "@com_google_absl//absl/strings:string_view",
    ],
)

cc_library(
    name = "ifrt_model_context",
    srcs = ["ifrt_model_context.cc"],
    hdrs = ["ifrt_model_context.h"],
    visibility = [
        "//learning/brain/tfrt:__subpackages__",
        "//learning/serving/servables/tfrt:__subpackages__",
        "//tensorflow/compiler/mlir/tfrt:__subpackages__",
        "//tensorflow/core/tfrt:__subpackages__",
        "//tensorflow/core/tfrt/mlrt:__subpackages__",
    ],
    deps = [
        ":ifrt_config_proto_cc",
        ":ifrt_executable_registry",
        ":ifrt_loaded_variable_registry",
        ":ifrt_persistent_compilation_cache",
        ":ifrt_restore_tensor_registry",
        ":ifrt_serving_core_selector",
        "//tensorflow/compiler/mlir/tfrt/transforms/ifrt:tf2hlo",
        "//tensorflow/compiler/tf2xla:xla_helpers",
        "//tensorflow/core:core_cpu_base",
        "@com_google_absl//absl/container:flat_hash_map",
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/strings",
        "@local_tsl//tsl/platform:protobuf",
        "@local_xla//xla/pjrt:pjrt_executable",
        "@local_xla//xla/python/ifrt",
        "@local_xla//xla/tsl/platform:env",
        "@local_xla//xla/tsl/platform:errors",
        "@tf_runtime//:hostcontext",
    ],
)

cc_library(
    name = "ifrt_tensor_utils",
    srcs = ["ifrt_tensor_utils.cc"],
    hdrs = ["ifrt_tensor_utils.h"],
    deps = [
        "//tensorflow/compiler/tf2xla:common",
        "//tensorflow/compiler/tf2xla:xla_helpers",
        "//tensorflow/core:framework",
        "//tensorflow/core:protos_all_cc",
        "//tensorflow/core/protobuf/tpu:compile_metadata_proto_cc",
        "@com_google_absl//absl/log",
        "@com_google_absl//absl/log:check",
        "@com_google_absl//absl/status:statusor",
        "@com_google_absl//absl/types:span",
        "@local_xla//xla:xla_data_proto_cc",
        "@local_xla//xla/python/ifrt",
        "@local_xla//xla/python/pjrt_ifrt:pjrt_dtype",
        "@local_xla//xla/tsl/platform:errors",
        "@local_xla//xla/tsl/platform:statusor",
    ],
)

cc_library(
    name = "sharding_utils",
    srcs = [
        "sharding_utils.cc",
    ],
    hdrs = [
        "sharding_utils.h",
    ],
    deps = [
        ":ifrt_tensor_utils",
        "//tensorflow/compiler/tf2xla:common",
        "//tensorflow/core:framework",
        "//tensorflow/core:protos_all_cc",
        "//tensorflow/core/tpu/kernels:sharding_utils",
        "@com_google_absl//absl/container:btree",
        "@com_google_absl//absl/container:inlined_vector",
        "@com_google_absl//absl/log",
        "@com_google_absl//absl/log:check",
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/status:statusor",
        "@com_google_absl//absl/strings",
        "@com_google_absl//absl/types:span",
        "@eigen_archive//:eigen3",
        "@local_xla//xla:shape_util",
        "@local_xla//xla/hlo/ir:hlo",
        "@local_xla//xla/python/ifrt",
        "@local_xla//xla/python/pjrt_ifrt:xla_ifrt",
        "@local_xla//xla/tsl/concurrency:ref_count",
        "@local_xla//xla/tsl/platform:env",
        "@local_xla//xla/tsl/platform:errors",
        "@local_xla//xla/tsl/platform:statusor",
    ],
)

cc_library(
    name = "ifrt_loaded_variable_utils",
    srcs = ["ifrt_loaded_variable_utils.cc"],
    hdrs = ["ifrt_loaded_variable_utils.h"],
    deps = [
        ":ifrt_config_proto_cc",
        ":ifrt_loaded_variable_registry",
        ":ifrt_restore_tensor_registry",
        ":sharding_utils",
        "//tensorflow/compiler/mlir/tfrt/transforms/ifrt:ifrt_types",
        "//tensorflow/core:framework",
        "@com_google_absl//absl/log",
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/status:statusor",
        "@com_google_absl//absl/strings",
        "@com_google_absl//absl/types:span",
        "@local_xla//xla/hlo/ir:hlo",
        "@local_xla//xla/python/ifrt",
        "@local_xla//xla/tsl/concurrency:ref_count",
        "@local_xla//xla/tsl/platform:env",
        "@local_xla//xla/tsl/platform:errors",
        "@local_xla//xla/tsl/platform:statusor",
        "@tf_runtime//:hostcontext",
    ],
)

cc_library(
    name = "tf_host_callback",
    srcs = ["tf_host_callback.cc"],
    hdrs = ["tf_host_callback.h"],
    deps = [
        "//tensorflow/c/eager:abstract_tensor_handle",
        "//tensorflow/c/eager:immediate_execution_context",
        "//tensorflow/c/eager:immediate_execution_operation",
        "//tensorflow/compiler/mlir/tfrt/transforms/ifrt:ifrt_types",
        "//tensorflow/core:core_cpu_base",
        "//tensorflow/core:framework",
        "//tensorflow/core:framework_internal",
        "//tensorflow/core/common_runtime/eager:context",
        "//tensorflow/core/common_runtime/eager:core_no_xla",
        "//tensorflow/core/common_runtime/eager:tensor_handle",
        "//tensorflow/core/framework:function_proto_cc",
        "//tensorflow/core/protobuf:for_core_protos_cc",
        "@com_google_absl//absl/cleanup",
        "@com_google_absl//absl/container:fixed_array",
        "@com_google_absl//absl/log:check",
        "@com_google_absl//absl/memory",
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/status:statusor",
        "@com_google_absl//absl/strings",
        "@com_google_absl//absl/types:span",
        "@local_tsl//tsl/platform:casts",
        "@local_tsl//tsl/platform:refcount",
        "@local_tsl//tsl/profiler/lib:traceme",
        "@local_xla//xla/tsl/platform:errors",
    ],
)

cc_library(
    name = "ifrt_serving_executable_test_util",
    testonly = True,
    srcs = ["ifrt_serving_executable_test_util.cc"],
    hdrs = ["ifrt_serving_executable_test_util.h"],
    data = [
        "//tensorflow/core/tfrt/ifrt/testdata",
    ],
    deps = [
        ":ifrt_loaded_variable_registry",
        ":ifrt_persistent_compilation_cache",
        ":ifrt_restore_tensor_registry",
        ":ifrt_serving_core_selector",
        ":ifrt_serving_executable",
        ":tf_host_callback",
        "//tensorflow/compiler/mlir/tensorflow",
        "//tensorflow/compiler/mlir/tfrt/transforms/ifrt:tf2hlo",
        "//tensorflow/compiler/tf2xla:xla_helpers",
        "//tensorflow/core:core_cpu_base",
        "//tensorflow/core/framework:tensor",
        "//tensorflow/core/framework:types_proto_cc",
        "//tensorflow/core/platform:resource_loader",
        "@com_google_absl//absl/status:statusor",
        "@com_google_absl//absl/strings",
        "@com_google_absl//absl/strings:string_view",
        "@llvm-project//mlir:AllPassesAndDialects",
        "@llvm-project//mlir:IR",
        "@llvm-project//mlir:Parser",
        "@local_xla//xla/python/ifrt",
        "@local_xla//xla/python/ifrt:test_util",
        "@local_xla//xla/tsl/framework/test_util:mock_serving_device_selector",
        "@local_xla//xla/tsl/platform:env",
        "@local_xla//xla/tsl/platform:status",
        "@tf_runtime//:hostcontext",
    ],
)

tf_cc_test(
    name = "ifrt_restore_tensor_registry_test",
    srcs = ["ifrt_restore_tensor_registry_test.cc"],
    tags = ["no_oss"],
    deps = [
        ":ifrt_restore_tensor_registry",
        "//tensorflow/compiler/mlir/tfrt/transforms/ifrt:ifrt_types",
        "//tensorflow/core:framework",
        "//tensorflow/core/framework:tensor",
        "//tensorflow/core/framework:tensor_testutil",
        "//tensorflow/core/framework:types_proto_cc",
        "@com_google_absl//absl/status",
        "@com_google_googletest//:gtest_main",
        "@local_xla//xla/python/ifrt",
        "@local_xla//xla/tsl/lib/core:status_test_util",
        "@local_xla//xla/tsl/platform:status_matchers",
        "@local_xla//xla/tsl/platform:statusor",
    ],
)

tf_cc_test(
    name = "ifrt_loaded_variable_utils_test",
    srcs = ["ifrt_loaded_variable_utils_test.cc"],
    tags = ["no_oss"],
    deps = [
        ":ifrt_config_proto_cc",
        ":ifrt_loaded_variable_registry",
        ":ifrt_loaded_variable_utils",
        ":ifrt_restore_tensor_registry",
        "//tensorflow/core:framework",
        "//tensorflow/core/framework:tensor_matcher",
        "//tensorflow/core/framework:tensor_testutil",
        "//tensorflow/core/framework:types_proto_cc",
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/status:statusor",
        "@com_google_googletest//:gtest_main",
        "@local_xla//xla:xla_data_proto_cc",
        "@local_xla//xla/hlo/ir:hlo",
        "@local_xla//xla/python/ifrt",
        "@local_xla//xla/python/ifrt:test_util",
        "@local_xla//xla/python/pjrt_ifrt:tfrt_cpu_client_test_lib",
        "@local_xla//xla/tsl/concurrency:ref_count",
        "@local_xla//xla/tsl/lib/core:status_test_util",
        "@local_xla//xla/tsl/platform:env",
        "@local_xla//xla/tsl/platform:status_matchers",
        "@local_xla//xla/tsl/platform:statusor",
        "@local_xla//xla/tsl/platform:test",
        "@tf_runtime//:hostcontext",
    ],
)

tf_cc_test(
    name = "tf_host_callback_test",
    srcs = ["tf_host_callback_test.cc"],
    tags = ["no_oss"],
    deps = [
        ":tf_host_callback",
        "//tensorflow/cc:const_op",
        "//tensorflow/cc:function_ops",
        "//tensorflow/cc:functional_ops",
        "//tensorflow/cc:math_ops",
        "//tensorflow/cc:ops",
        "//tensorflow/cc:resource_variable_ops",
        "//tensorflow/cc:scope",
        "//tensorflow/compiler/mlir/tfrt/transforms/ifrt:ifrt_types",
        "//tensorflow/core:core_cpu_base",
        "//tensorflow/core:framework",
        "//tensorflow/core:protos_all_cc",
        "//tensorflow/core:tensorflow",
        "//tensorflow/core/framework:tensor_matcher",
        "//tensorflow/core/framework:tensor_testutil",
        "//tensorflow/core/kernels:constant_op",
        "//tensorflow/core/kernels:math",
        "@com_google_absl//absl/status:statusor",
        "@com_google_absl//absl/strings:string_view",
        "@com_google_googletest//:gtest_main",
        "@local_xla//xla:xla_data_proto_cc",
        "@local_xla//xla/tsl/platform:errors",
        "@local_xla//xla/tsl/platform:statusor",
    ],
)

tf_cc_test(
    name = "sharding_utils_test",
    srcs = ["sharding_utils_test.cc"],
    tags = ["no_oss"],
    deps = [
        ":ifrt_tensor_utils",
        ":pjrt_cpu_client_test_lib",
        ":sharding_utils",
        "//tensorflow/core:framework",
        "//tensorflow/core:test",
        "//tensorflow/core/framework:tensor_matcher",
        "//tensorflow/core/framework:tensor_testutil",
        "//tensorflow/core/framework:types_proto_cc",
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/strings",
        "@com_google_absl//absl/types:span",
        "@com_google_googletest//:gtest_main",
        "@local_tsl//tsl/platform:ml_dtypes",
        "@local_xla//xla:xla_data_proto_cc",
        "@local_xla//xla/hlo/ir:hlo",
        "@local_xla//xla/python/ifrt",
        "@local_xla//xla/python/ifrt:test_util",
        "@local_xla//xla/python/pjrt_ifrt:xla_ifrt",
        "@local_xla//xla/tsl/concurrency:ref_count",
        "@local_xla//xla/tsl/platform:env",
        "@local_xla//xla/tsl/platform:status_matchers",
        "@local_xla//xla/tsl/platform:statusor",
    ],
)

tf_cc_test(
    name = "ifrt_serving_executable_test",
    srcs = [
        "ifrt_serving_executable_test.cc",
    ],
    data = [
        "//tensorflow/core/tfrt/ifrt/testdata",
    ],
    tags = ["no_oss"],
    deps = [
        ":ifrt_restore_tensor_registry",
        ":ifrt_serving_executable",
        ":ifrt_serving_executable_test_util",
        "//tensorflow/compiler/tf2xla:xla_helpers",
        "//tensorflow/core:core_cpu_base",
        "//tensorflow/core:framework",
        "//tensorflow/core:portable_tensorflow_test_lib",
        "//tensorflow/core:test",
        "//tensorflow/core/common_runtime/eager:core",
        "//tensorflow/core/framework:tensor",
        "//tensorflow/core/framework:tensor_matcher",
        "//tensorflow/core/framework:tensor_testutil",
        "//tensorflow/core/framework:types_proto_cc",
        "@com_google_absl//absl/log:check",
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/status:statusor",
        "@com_google_absl//absl/strings",
        "@com_google_absl//absl/types:span",
        "@com_google_googletest//:gtest_main",
        "@local_tsl//tsl/platform:tstring",
        "@local_xla//xla/python/ifrt",
        "@local_xla//xla/python/ifrt:test_util",
        "@local_xla//xla/python/pjrt_ifrt:tfrt_cpu_client_test_lib",
        "@local_xla//xla/tsl/framework:serving_device_selector",
        "@local_xla//xla/tsl/framework/test_util:mock_serving_device_selector",
        "@local_xla//xla/tsl/platform:statusor",
        "@tf_runtime//:basic_kernels_alwayslink",
        "@tf_runtime//:core_runtime_alwayslink",
        "@tf_runtime//:test_kernels_alwayslink",
        "@tf_runtime//backends/cpu:core_runtime_alwayslink",
        "@tf_runtime//backends/cpu:tf_ops_alwayslink",
    ],
)

tf_cc_test(
    name = "ifrt_executable_registry_test",
    srcs = [
        "ifrt_executable_registry_test.cc",
    ],
    data = [
        "//tensorflow/core/tfrt/ifrt/testdata",
    ],
    tags = ["no_oss"],
    deps = [
        ":ifrt_executable_registry",
        ":ifrt_loaded_variable_registry",
        ":ifrt_restore_tensor_registry",
        ":ifrt_serving_executable",
        ":tf_host_callback",
        "//tensorflow/compiler/mlir/tensorflow",
        "//tensorflow/compiler/tf2xla:xla_helpers",
        "//tensorflow/core:core_cpu_base",
        "//tensorflow/core:lib",
        "//tensorflow/core:portable_tensorflow_test_lib",
        "//tensorflow/core:test",
        "//tensorflow/core/common_runtime/eager:core",
        "//tensorflow/core/framework:tensor",
        "//tensorflow/core/framework:types_proto_cc",
        "//tensorflow/core/platform:resource_loader",
        "//tensorflow/core/platform:status_matchers",
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/status:statusor",
        "@com_google_absl//absl/strings",
        "@com_google_googletest//:gtest_main",
        "@llvm-project//mlir:AllPassesAndDialects",
        "@llvm-project//mlir:IR",
        "@llvm-project//mlir:Parser",
        "@local_xla//xla/python/ifrt",
        "@local_xla//xla/python/ifrt:test_util",
        "@local_xla//xla/python/pjrt_ifrt:tfrt_cpu_client_test_lib",
        "@local_xla//xla/tsl/platform:env",
        "@local_xla//xla/tsl/platform:statusor",
        "@tf_runtime//:basic_kernels_alwayslink",
        "@tf_runtime//:core_runtime_alwayslink",
        "@tf_runtime//:hostcontext",
        "@tf_runtime//:test_kernels_alwayslink",
        "@tf_runtime//backends/cpu:core_runtime_alwayslink",
        "@tf_runtime//backends/cpu:tf_ops_alwayslink",
    ],
)

cc_library(
    name = "checkpoint_loader",
    srcs = ["checkpoint_loader.cc"],
    hdrs = ["checkpoint_loader.h"],
    deps = [
        ":ifrt_loaded_variable_utils",
        ":ifrt_restore_tensor_registry",
        "//tensorflow/compiler/mlir/tfrt/transforms/ifrt:ifrt_types",
        "//tensorflow/core:framework",
        "//tensorflow/core:lib",
        "//tensorflow/core/common_runtime:function",
        "//tensorflow/core/framework:attr_value_proto_cc",
        "//tensorflow/core/framework:node_def_util",
        "//tensorflow/core/framework:tensor",
        "//tensorflow/core/framework:types_proto_cc",
        "//tensorflow/core/protobuf:for_core_protos_cc",
        "//tensorflow/core/tfrt/fallback:fallback_state",
        "//tensorflow/core/tfrt/fallback:op_kernel_runner",
        "//tensorflow/core/tfrt/mlrt/bytecode",
        "//tensorflow/core/tfrt/mlrt/kernel:context",
        "//tensorflow/core/tfrt/mlrt/kernel:kernel_runner_utils",
        "//tensorflow/core/tfrt/mlrt/kernel:shard_restore_util",
        "//tensorflow/core/tfrt/utils:fallback_tensor",
        "@com_google_absl//absl/container:flat_hash_map",
        "@com_google_absl//absl/log",
        "@com_google_absl//absl/log:check",
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/status:statusor",
        "@com_google_absl//absl/strings",
        "@com_google_absl//absl/types:span",
        "@llvm-project//mlir:IR",
        "@local_tsl//tsl/platform:tstring",
        "@local_xla//xla/python/ifrt",
        "@local_xla//xla/tsl/platform:errors",
        "@local_xla//xla/tsl/platform:statusor",
        "@tf_runtime//:hostcontext",
    ],
)

cc_library(
    name = "pjrt_cpu_client_test_lib",
    testonly = True,
    srcs = ["pjrt_cpu_client_test_lib.cc"],
    deps = [
        "@com_google_absl//absl/status:statusor",
        "@local_xla//xla/pjrt/plugin/xla_cpu:cpu_client_options",
        "@local_xla//xla/pjrt/plugin/xla_cpu:xla_cpu_pjrt_client",
        "@local_xla//xla/python/ifrt",
        "@local_xla//xla/python/ifrt:test_util",
        "@local_xla//xla/python/pjrt_ifrt",
        "@local_xla//xla/tsl/platform:statusor",
    ],
    alwayslink = True,
)
