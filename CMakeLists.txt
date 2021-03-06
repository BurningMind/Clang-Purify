cmake_minimum_required(VERSION 3.3)
project(Clang-Purify)

set(CLANG_LIB "/usr/local")
set(LLVM_PATH "${CLANG_LIB}")

SET (CMAKE_C_COMPILER "${CLANG_LIB}/bin/clang")
SET (CMAKE_CXX_COMPILER "${CLANG_LIB}/bin/clang++")


EXECUTE_PROCESS( COMMAND ${CMAKE_CXX_COMPILER} --version OUTPUT_VARIABLE clang_full_version_string)
string (REGEX REPLACE ".*clang version ([0-9]+\\.[0-9]+).*" "\\1" CLANG_VERSION_STRING ${clang_full_version_string})


if (CLANG_VERSION_STRING VERSION_LESS 3.8)
    message(FATAL_ERROR "Insufficient clang version - need at least 3.8")
endif()


SET (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -g -std=c++11 -fno-rtti  -stdlib=libc++ -v")

add_definitions(
        -D__STDC_LIMIT_MACROS
        -D__STDC_CONSTANT_MACROS
)

include_directories(${LLVM_PATH}/include)
include_directories(include)

link_directories(${LLVM_PATH}/lib)


file(GLOB_RECURSE SOURCE_FILES "src/*.cpp")
file(GLOB_RECURSE INCLUDE_FILES "include/*.h")

file(GLOB_RECURSE TEST_FILES "tests/*.cpp")

add_executable(Clang-Purify ${INCLUDE_FILES} ${SOURCE_FILES} ${TEST_FILES})


# get full path for test files
get_filename_component(TEST_FILES_FULL_PATH "tests/" ABSOLUTE)

# add a definition in project with full path 
target_compile_definitions(Clang-Purify PRIVATE TEST_FILES_PATH="${TEST_FILES_FULL_PATH}")


if(LINUX) 

target_link_libraries(Clang-Purify
        ffi
        edit
        m
        clangFrontend
        clangFrontendTool
        clangSerialization
        clangDriver
        clangCodeGen # 1
        clangParse
        clangSema
        clangStaticAnalyzerCheckers # 2
        clangStaticAnalyzerCore #3
        clangAnalysis
        clangIndex
        clangRewrite
        clangAST
        clangASTMatchers
        clangBasic
        clangEdit
        clangLex
        clangTooling
        LLVMX86AsmParser # MC, MCParser, Support, X86Desc, X86Info
        LLVMX86Desc # MC, Support, X86AsmPrinter, X86Info
        LLVMX86AsmPrinter # MC, Support, X86Utils
        LLVMX86Info # MC, Support, Target
        LLVMX86Utils # Core, Support
        LLVMipo
        LLVMScalarOpts
        LLVMInstCombine
        LLVMTransformUtils
        LLVMAnalysis
        LLVMTarget
        LLVMOption # Support
        LLVMMCParser # MC, Support
        LLVMMC # Object, Support
        LLVMObject # BitReader, Core, Support
        LLVMBitReader # Core, Support
        LLVMCore
        LLVMSupport
        z
        tinfo
        dl
        pthread
)

endif()

if(APPLE) 

target_link_libraries(Clang-Purify
        ffi
        edit
        ncurses
        m
        clangFrontend
        clangFrontendTool
        clangSerialization
        clangDriver
        clangCodeGen # 1
        clangParse
        clangSema
        clangStaticAnalyzerCheckers # 2
        clangStaticAnalyzerCore #3
        clangAnalysis
        clangIndex
        clangRewrite
        clangAST
        clangASTMatchers
        clangBasic
        clangEdit
        clangLex
        clangTooling
        LLVMX86AsmParser # MC, MCParser, Support, X86Desc, X86Info
        LLVMX86Desc # MC, Support, X86AsmPrinter, X86Info
        LLVMX86AsmPrinter # MC, Support, X86Utils
        LLVMX86Info # MC, Support, Target
        LLVMX86Utils # Core, Support
        LLVMipo
        LLVMScalarOpts
        LLVMInstCombine
        LLVMTransformUtils
        LLVMAnalysis
        LLVMTarget
        LLVMOption # Support
        LLVMMCParser # MC, Support
        LLVMMC # Object, Support
        LLVMObject # BitReader, Core, Support
        LLVMBitReader # Core, Support
        LLVMCore
        LLVMSupport
        LLVMProfileData
        LLVMDemangle
        z
        dl
        pthread
)

endif()

