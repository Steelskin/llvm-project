set(LLVM_LINK_COMPONENTS
  DebugInfoDWARF
  DebugInfoDWARFLowLevel
  Demangle
  MC
  MCDisassembler
  Object
  Support
  BinaryFormat
  AsmPrinter
  TargetParser
  # Not used by BOLT directly. MSVC emits the vtable and the inline members of
  # every dllexport class in each translation unit that sees it, so in an
  # LLVM_BUILD_LLVM_DYLIB build with cl.exe the objects of LLVMCodeGen (pulled
  # in by AsmPrinter) reference SelectionDAG and TargetLowering members that
  # live in LLVMSelectionDAG, and those of LLVMAsmPrinter reference a pass
  # constructor in LLVMInstrumentation. Tools that link a target get both
  # through the target's own components; llvm-bat-dump does not link one. BOLT
  # links the LLVM components statically (DISABLE_LLVM_LINK_LLVM_DYLIB below),
  # so it has to supply them itself. Elsewhere these archives contribute
  # nothing.
  SelectionDAG
  Instrumentation
  )

add_llvm_library(LLVMBOLTCore
  AddressMap.cpp
  BinaryBasicBlock.cpp
  BinaryContext.cpp
  BinaryData.cpp
  BinaryEmitter.cpp
  BinaryFunction.cpp
  BinaryFunctionCallGraph.cpp
  BinaryFunctionProfile.cpp
  BinarySection.cpp
  BranchLivenessInfo.cpp
  CallGraph.cpp
  CallGraphWalker.cpp
  DebugData.cpp
  DebugNames.cpp
  DIEBuilder.cpp
  DynoStats.cpp
  Exceptions.cpp
  FunctionLayout.cpp
  GDBIndex.cpp
  HashUtilities.cpp
  JumpTable.cpp
  MCInstUtils.cpp
  MCPlusBuilder.cpp
  ParallelUtilities.cpp
  Relocation.cpp

  NO_EXPORT
  DISABLE_LLVM_LINK_LLVM_DYLIB
  LINK_LIBS
  ${LLVM_PTHREAD_LIB}
  )

target_link_libraries(LLVMBOLTCore
  PRIVATE
  LLVMBOLTUtils
  )
