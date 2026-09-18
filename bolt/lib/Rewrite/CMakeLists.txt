set(LLVM_LINK_COMPONENTS
  Core
  DebugInfoDWARF
  DebugInfoDWARFLowLevel
  JITLink
  MC
  Object
  Support
  DWARFLinker
  DWARFLinkerClassic
  AsmPrinter
  TargetParser
  # Not used by BOLT directly. JITLink.h includes Orc/Core.h, and MSVC emits
  # the vtable of every dllexport class in each translation unit that sees
  # it, so in an LLVM_BUILD_LLVM_DYLIB build with cl.exe the objects of
  # LLVMJITLink reference ORC and RuntimeDyld virtuals. BOLT links the LLVM
  # components statically (DISABLE_LLVM_LINK_LLVM_DYLIB below), so it has to
  # supply them itself. Elsewhere these archives contribute nothing.
  OrcJIT
  RuntimeDyld
  )

add_llvm_library(LLVMBOLTRewrite
  BinaryPassManager.cpp
  BoltDiff.cpp
  DWARFRewriter.cpp
  ExecutableFileMemoryManager.cpp
  JITLinkLinker.cpp
  LinuxKernelRewriter.cpp
  MachORewriteInstance.cpp
  MetadataManager.cpp
  BuildIDRewriter.cpp
  PseudoProbeRewriter.cpp
  RewriteInstance.cpp
  RSeqRewriter.cpp
  SDTRewriter.cpp
  GNUPropertyRewriter.cpp

  NO_EXPORT
  DISABLE_LLVM_LINK_LLVM_DYLIB

  LINK_LIBS
  ${LLVM_PTHREAD_LIB}
  )

target_link_libraries(LLVMBOLTRewrite
  PRIVATE
  LLVMBOLTCore
  LLVMBOLTPasses
  LLVMBOLTProfile
  LLVMBOLTRuntimeLibs
  LLVMBOLTUtils
  )

foreach (tgt ${BOLT_TARGETS_TO_BUILD})
  target_link_libraries(LLVMBOLTRewrite PRIVATE LLVMBOLTTarget${tgt})
  string(TOUPPER "${tgt}" upper)
  target_compile_definitions(LLVMBOLTRewrite PRIVATE ${upper}_AVAILABLE)
endforeach()
