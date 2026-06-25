# OrcJIT is needed even though this tool does not use the JIT directly:
# OrcRTBootstrap.cpp in OrcTargetProcess references symbols from Orc/Core.cpp
# and Orc/TaskDispatch.cpp. OrcTargetProcess cannot declare that dependency
# itself, as OrcJIT already depends on OrcTargetProcess. Other consumers do not
# notice because they resolve these through libLLVM, but add_llvm_utility forces
# DISABLE_LLVM_LINK_LLVM_DYLIB, so this tool always links component libraries.
set(LLVM_LINK_COMPONENTS
  ExecutionEngine
  OrcJIT
  OrcShared
  OrcTargetProcess
  Support
  )

add_llvm_utility(llvm-jitlink-executor
  llvm-jitlink-executor.cpp

  DEPENDS
  intrinsics_gen

  EXPORT_SYMBOLS
)
