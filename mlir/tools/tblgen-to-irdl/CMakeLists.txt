set(LLVM_LINK_COMPONENTS
  Support
  TableGen
)

add_tablegen(tblgen-to-irdl MLIR
  LINK_LLVM_DYLIB
  DESTINATION "${MLIR_TOOLS_INSTALL_DIR}"
  EXPORT MLIR
  tblgen-to-irdl.cpp
  OpDefinitionsGen.cpp
  )

target_link_libraries(tblgen-to-irdl
  PRIVATE
  MLIRIR
  MLIRIRDL
  MLIRTblgenLib
  MLIRSupport
)

mlir_check_all_link_libraries(tblgen-to-irdl)
