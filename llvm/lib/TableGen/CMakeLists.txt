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
set(LLVM_TABLEGEN_SOURCES
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
  )

add_llvm_library(LLVMTableGen
  ${LLVM_TABLEGEN_SOURCES}

  DISABLE_LLVM_LINK_LLVM_DYLIB

  ADDITIONAL_HEADER_DIRS
  ${LLVM_MAIN_INCLUDE_DIR}/llvm/TableGen
  )

if(LLVM_LINK_LLVM_DYLIB)
  # LLVMTableGen above is built as if LLVM were static (DISABLE_LLVM_LINK_LLVM_DYLIB
  # implies LLVM_BUILD_STATIC): the *-tblgen utilities that generate libLLVM's own
  # sources cannot link libLLVM, so that is the flavour they need. A tool that does
  # link libLLVM must not mix that flavour in. Its other objects import LLVM's API,
  # and on MSVC the inline members libLLVM exports (vtable slots inherited from
  # raw_ostream, cl::Option, ...) then reach the link line twice: as import thunks
  # from LLVM.lib and as the local definitions a LLVM_BUILD_STATIC object emits for
  # itself, which link.exe rejects with LNK2005. clang-cl only hides this because
  # /Zc:dllexportInlines- keeps those members out of the import library.
  #
  # So build a second flavour that imports from libLLVM, and register it so that
  # llvm_filter_non_component_libs() substitutes it for every consumer that links
  # libLLVM (see LLVM-Config.cmake). This mirrors clangSupport_tablegen in
  # clang/lib/Support, with the roles reversed.
  add_llvm_library(LLVMTableGen_dylib
    ${LLVM_TABLEGEN_SOURCES}

    ADDITIONAL_HEADER_DIRS
    ${LLVM_MAIN_INCLUDE_DIR}/llvm/TableGen
    )
  set_property(GLOBAL PROPERTY LLVM_DYLIB_MODE_LIB_LLVMTableGen LLVMTableGen_dylib)
endif()
