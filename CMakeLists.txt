cmake_minimum_required(VERSION 3.31.6)
project(riscvpp)

set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

add_library(riscv_core
        src/core/cpu.cpp
        include/riscvpp/core/cpu.h
        include/riscvpp/core/register.h
        include/riscvpp/core/instruction.h
        include/riscvpp/parser/parser.h
)

target_compile_options(riscv_core PRIVATE
        -Wall -Wextra          # Enable all warnings
        -Werror                # Warnings are errors
        -Wpedantic             # Enforce strict ISO C++ compliance
        -Wconversion           # Warn if implicit conversions lose data
        -Wsign-conversion      # Warn if implicit conversions change sign
        -Wshadow               # Warn if a local variable shadows another variable
        -Wold-style-cast       # Ban C-style casts
        -Wunused               # Warn on unused stuff
)

# Helping C++ be a little bit more safe
target_compile_options(riscv_core PRIVATE -fsanitize=address,undefined)
target_link_options(riscv_core PRIVATE -fsanitize=address,undefined)

target_include_directories(riscv_core PUBLIC include)

add_executable(riscvpp main.cpp
        include/riscvpp/parser/parser.h)
target_link_libraries(riscvpp PRIVATE riscv_core)

enable_testing()

add_executable(test_register tests/core/register_tests.cpp)
target_link_libraries(test_register PRIVATE riscv_core)

add_executable(test_instruction tests/core/instruction_tests.cpp)
target_link_libraries(test_instruction PRIVATE riscv_core)

add_test(NAME RegisterTest COMMAND test_register)