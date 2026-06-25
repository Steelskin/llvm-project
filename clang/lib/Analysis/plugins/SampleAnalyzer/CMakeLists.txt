set(LLVM_LINK_COMPONENTS
  Support
  )

set(LLVM_EXPORTED_SYMBOL_FILE ${CMAKE_CURRENT_SOURCE_DIR}/SampleAnalyzerPlugin.exports)

clang_get_abi_definition(clang_abi_def MODULE)
set(clang_pch_abi_arg "")
if(clang_abi_def)
  set(clang_pch_abi_arg PCH_ABI_DEFINITION ${clang_abi_def})
endif()
add_llvm_library(SampleAnalyzerPlugin
  MODULE
  BUILDTREE_ONLY
  ${clang_pch_abi_arg}

  MainCallChecker.cpp
  )

clang_target_link_libraries(SampleAnalyzerPlugin PRIVATE
  clangAnalysis
  clangAST
  clangStaticAnalyzerCore
  clangStaticAnalyzerFrontend
  )
