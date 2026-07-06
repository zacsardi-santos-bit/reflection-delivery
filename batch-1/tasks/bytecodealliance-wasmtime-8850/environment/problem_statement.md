## Description

The Cranelift compiler crashes with an internal compiler error when compiling functions for RISC-V64 that combine highly-aligned stack storage with indirect tail calls. This means any program that uses stack slots requiring large alignment (e.g., 512 or 1024 bytes) alongside an indirect tail call cannot be compiled at all on RISC-V64.

## Steps to Reproduce

Compile a RISC-V64 function that:
- Declares explicit stack slots with alignment requirements of 512 bytes or larger
- Performs an indirect tail call at the end

The compiler will panic / produce an internal compiler error instead of successfully generating code.

## Expected Behavior

- Functions with large-alignment stack slots and indirect tail calls should compile successfully on RISC-V64 without any internal errors.
- Complex functions combining conditional branches, branch tables, large-alignment stack slots, and indirect tail calls should also compile without errors.
- Conditional branches in such functions whose targets are beyond the native range of RISC-V conditional branch instructions should be correctly expanded into multi-instruction sequences that preserve the intended control flow.

## Why This Matters

This regression prevents valid RISC-V64 programs from compiling whenever they combine alignment-sensitive stack data with tail call patterns, blocking users on RISC-V64 platforms who rely on Cranelift for code generation.
