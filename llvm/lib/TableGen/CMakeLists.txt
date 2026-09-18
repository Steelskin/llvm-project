# Note: This isn't a component. TableGen registers cl::opt options during
# static initialization; rolling it into libLLVM would make every one of them
# a global option of the dylib and of everything linking it. It is also only
# ever used by the *-tblgen utilities.
#
# Because it sits outside libLLVM but is linked into tools that use libLLVM,
# it must not name any LLVM library in its own link interface: a consumer may
# get Support from libLLVM or from LLVMSupport, and naming either here would
# put both copies on somebody's link line. Consumers request Support for
# themselves. This mirrors mlir/lib/TableGen.
add_llvm_library(LLVMTableGen
  DetailedRecordsBackend.cpp
  Error.cpp
  JSONBackend.cpp
  Main.cpp
  Parser.cpp
  Record.cpp
  SetTheory.cpp
  StringMatcher.cpp
  StringToOffsetTable.cpp
  TableGenBackend.cpp
  TableGenBackendSkeleton.cpp
  TGLexer.cpp
  TGParser.cpp
  TGTimer.cpp

  DISABLE_LLVM_LINK_LLVM_DYLIB

  ADDITIONAL_HEADER_DIRS
  ${LLVM_MAIN_INCLUDE_DIR}/llvm/TableGen
  )
