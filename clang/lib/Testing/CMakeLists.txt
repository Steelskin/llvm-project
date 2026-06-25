# Don't include this target in LLVMExports. FIXME: is there a cleaner way?
set(EXCLUDE_FROM_ALL ON)

# Not add_clang_library: this is not part of clang's public library interface.
# Unit tests should depend on this with target_link_libraries(), rather
# than with clang_target_link_libraries().
#
# It still inherits clang's ABI define from the clang libraries it links below,
# so it needs the matching PCH key even though it is created by the LLVM helper.
clang_get_abi_definition(clang_abi_def LIBRARY)
set(clang_pch_abi_arg "")
if(clang_abi_def)
  set(clang_pch_abi_arg PCH_ABI_DEFINITION ${clang_abi_def})
endif()

add_llvm_library(clangTesting
  CommandLineArgs.cpp
  TestAST.cpp

  BUILDTREE_ONLY
  ${clang_pch_abi_arg}

  LINK_COMPONENTS
  MC
  Support
  TargetParser
  )

clang_target_link_libraries(clangTesting
  PRIVATE
  clangAST
  clangBasic
  clangFrontend
  clangLex
  clangSerialization
  )

target_link_libraries(clangTesting
  PRIVATE
  llvm_gtest
  )
