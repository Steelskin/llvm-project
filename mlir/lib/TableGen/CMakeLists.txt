# This library is linked into tools that also link libLLVM (mlir-pdll and
# tblgen-to-irdl, for instance), so it has to import from libLLVM rather than
# assume a static LLVM: a translation unit built with LLVM_BUILD_STATIC emits
# plain references to exported data such as llvm::DebugFlag, and an import
# library provides only __imp_ for data, with no thunk to fall back on.
#
# It must still not depend on libraries that may be transitively included in
# libMLIR.so; specifically, MLIRSupport (outside of header-only access) cannot
# be used.
llvm_add_library(MLIRTableGen STATIC
  Argument.cpp
  Attribute.cpp
  AttrOrTypeDef.cpp
  Builder.cpp
  Class.cpp
  CodeGenHelpers.cpp
  Constraint.cpp
  Dialect.cpp
  EnumInfo.cpp
  Format.cpp
  GenInfo.cpp
  Interfaces.cpp
  Operator.cpp
  Pass.cpp
  Pattern.cpp
  Predicate.cpp
  Property.cpp
  Region.cpp
  SideEffects.cpp
  Successor.cpp
  Trait.cpp
  Type.cpp

  ADDITIONAL_HEADER_DIRS
  ${MLIR_MAIN_INCLUDE_DIR}/mlir/TableGen
  ${MLIR_MAIN_INCLUDE_DIR}/mlir/Support
)
set_target_properties(MLIRTableGen PROPERTIES FOLDER "MLIR/Tablegenning")

mlir_check_all_link_libraries(MLIRTableGen)

add_mlir_library_install(MLIRTableGen)
