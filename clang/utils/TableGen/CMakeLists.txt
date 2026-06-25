set(LLVM_LINK_COMPONENTS Support)

clang_get_abi_definition(clang_abi_def EXECUTABLE)
set(clang_pch_abi_arg "")
if(clang_abi_def)
  set(clang_pch_abi_arg PCH_ABI_DEFINITION ${clang_abi_def})
endif()
add_tablegen(clang-tblgen CLANG
  DESTINATION "${CLANG_TOOLS_INSTALL_DIR}"
  EXPORT Clang
  ${clang_pch_abi_arg}
  ASTTableGen.cpp
  CIRLoweringEmitter.cpp
  ClangASTNodesEmitter.cpp
  ClangASTPropertiesEmitter.cpp
  ClangAttrEmitter.cpp
  ClangBuiltinsEmitter.cpp
  ClangBuiltinTemplatesEmitter.cpp
  ClangCommentCommandInfoEmitter.cpp
  ClangCommentHTMLNamedCharacterReferenceEmitter.cpp
  ClangCommentHTMLTagsEmitter.cpp
  ClangDataCollectorsEmitter.cpp
  ClangDiagnosticsEmitter.cpp
  ClangOpcodesEmitter.cpp
  ClangOpenCLBuiltinEmitter.cpp
  ClangOptionDocEmitter.cpp
  ClangSACheckersEmitter.cpp
  ClangSyntaxEmitter.cpp
  ClangTypeNodesEmitter.cpp
  ClangTraitsEmitter.cpp
  HLSLEmitter.cpp
  MveEmitter.cpp
  NeonEmitter.cpp
  RISCVVEmitter.cpp
  SveEmitter.cpp
  TableGen.cpp
  )

target_link_libraries(clang-tblgen PRIVATE clangSupport_tablegen)
