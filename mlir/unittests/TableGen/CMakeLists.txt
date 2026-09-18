set(LLVM_TARGET_DEFINITIONS enums.td)
mlir_tablegen(EnumsGenTest.h.inc -gen-enum-decls)
mlir_tablegen(EnumsGenTest.cpp.inc -gen-enum-defs)
add_public_tablegen_target(MLIRTableGenEnumsIncGen)

set(LLVM_TARGET_DEFINITIONS passes.td)
mlir_tablegen(PassGenTest.h.inc -gen-pass-decls -name TableGenTest)
add_public_tablegen_target(MLIRTableGenTestPassIncGen)

# LLVMTableGen is not part of libLLVM. Request it as a component rather than by
# name so that the flavour matching this test's own link mode is picked; see
# llvm/lib/TableGen/CMakeLists.txt.
set(LLVM_LINK_COMPONENTS TableGen)

add_mlir_unittest(MLIRTableGenTests
  EnumsGenTest.cpp
  FormatTest.cpp
  OpBuildGen.cpp
  PassGenTest.cpp
)

add_dependencies(MLIRTableGenTests MLIRTableGenEnumsIncGen)
add_dependencies(MLIRTableGenTests MLIRTableGenTestPassIncGen)
add_dependencies(MLIRTableGenTests MLIRTestDialect)

target_include_directories(MLIRTableGenTests
  PRIVATE
  ${CMAKE_CURRENT_SOURCE_DIR}/../../test/lib/Dialect/Test
  ${CMAKE_CURRENT_BINARY_DIR}/../../test/lib/Dialect/Test
)

# MLIRIR has to come from libMLIR when that is in use (as it does for
# MLIRTestDialect), or the test ends up with a second copy of the MLIR core and
# attribute storage lookups fail at runtime. MLIRTableGen is never part of
# libMLIR, so it is linked directly.
mlir_target_link_libraries(MLIRTableGenTests PRIVATE MLIRIR)
target_link_libraries(MLIRTableGenTests
  PRIVATE MLIRTableGen
  PUBLIC MLIRTestDialect
)
