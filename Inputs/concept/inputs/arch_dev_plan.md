# Architecture Specifics (`arch`) Development Plan

## Overview

This document details the development plan for the Architecture Specifics (`arch`) subsystem of GritOS. This module is located at `src/arch`. It is responsible for all architecture-dependent code, providing a clear abstraction layer for the rest of the kernel to facilitate portability across multiple CPU architectures.

## Supported Architectures

GritOS aims to support the following architectures:

*   **x86_64:** The initial target architecture.
*   **ARM64 (AArch64):** Planned as the second target architecture.

## Core Responsibilities of the `arch` Module

The `arch` module, for each supported architecture, will be responsible for:

*   **Bootstrapping:** Early CPU initialization, setting up minimal execution environment.
*   **Memory Management Unit (MMU) Configuration:** Setting up page tables, managing virtual address spaces specific to the architecture.
*   **Interrupt and Exception Handling:** Configuring interrupt controllers, defining interrupt service routines (ISRs), and handling CPU exceptions.
*   **Context Switching:** Primitives for saving and restoring CPU state for task switching.
*   **Atomic Operations & Synchronization Primitives:** Providing architecture-specific implementations if not fully covered by `core::sync::atomic`.
*   **Timers:** Interfacing with architecture-specific timers (e.g., PIT/HPET for x86_64, ARM architected timer for ARM64).
*   **System Call Interface:** Low-level mechanics for system call entry and exit.
*   **Inter-Processor Communication (for SMP):** If Symmetric Multiprocessing (SMP) is supported.

## Directory Structure within `src/arch/`

To manage code for different architectures, the following structure will be used:

*   **`src/arch/x86_64/`:** Contains all code specific to the x86_64 architecture.
    *   `mod.rs`, `boot.rs`, `memory.rs` (for paging specifics), `interrupts.rs`, `cpu.rs`, `syscall.rs`, etc.
*   **`src/arch/aarch64/`:** Will contain all code specific to the ARM64 (AArch64) architecture.
    *   Similar substructure: `mod.rs`, `boot.rs`, `memory.rs`, `interrupts.rs`, `cpu.rs`, `syscall.rs`, etc.
*   **`src/arch/common/` (Optional):** May contain code or definitions shared across architectures, if any.
*   **`src/arch/mod.rs`:** Will use conditional compilation (`#[cfg(target_arch = "...")]`) to expose the correct architecture's public interface to the rest of the kernel. For example:
    ```rust
    #[cfg(target_arch = "x86_64")]
    pub use self::x86_64::*;
    #[cfg(target_arch = "aarch64")]
    pub use self::aarch64::*;
    // Potentially common definitions or traits here
    ```

## Common Architectural Abstractions (Traits)

To promote portable kernel code, the `arch` module will aim to define and implement a set of common traits that abstract architecture-specific operations. Examples (conceptual):

```rust
pub trait InterruptController {
    fn init(&mut self);
    fn enable_irq(&mut self, irq: u8);
    fn disable_irq(&mut self, irq: u8);
    fn end_of_interrupt(&mut self, irq: u8);
}

pub trait Timer {
    fn init(&mut self, frequency: u32); // Frequency in Hz
    fn set_handler(&mut self, handler: fn()); // Simple handler for now
}

pub trait Cpu {
    fn halt(&self);
    fn id(&self) -> u32; // For SMP
    // Other CPU specific functions like enabling/disabling interrupts globally
}

pub trait Mmu {
    type PageTable; // Architecture-specific page table type
    fn current_page_table(&self) -> Self::PageTable;
    fn switch_page_table(&self, table: &Self::PageTable);
    // Functions for mapping, unmapping, translating addresses etc.
}
```
These traits will be implemented by the respective architecture-specific modules (`x86_64`, `aarch64`).

## Development Phases & Architecture-Specific Considerations

Development will proceed by first maturing x86_64 support, then implementing ARM64 support.

### x86_64 Architecture:

*   **Phase 1 (Initial Boot & Core Setup - In Progress):**
    *   Bootloader integration (`bootimage`).
    *   GDT, IDT, basic exception handlers.
    *   PIC/APIC setup for interrupt handling.
    *   PIT/HPET for timer interrupts.
    *   Basic paging setup.
    *   Serial and VGA console output.
*   **Phase 2 (Context Switching & System Calls):**
    *   Implement task switching logic.
    *   Low-level system call entry/exit mechanisms (`syscall`/`sysret`).
*   **Phase 3 (Advanced Features):**
    *   SMP support (AP startup, IPIs).
    *   Advanced CPU features (SIMD state saving, etc.).

### ARM64 (AArch64) Architecture:

*   **Phase A1 (Research & Initial Boot):**
    *   Boot protocol research (e.g., UEFI, device tree).
    *   Toolchain setup for AArch64 bare-metal target.
    *   Minimal assembly startup code for AArch64.
    *   Setting up exception levels (e.g., EL1 for kernel).
    *   Basic MMU setup (identity mapping kernel).
    *   Serial console output (e.g., PL011 UART).
*   **Phase A2 (Interrupts & Timers):**
    *   GIC (Generic Interrupt Controller) setup.
    *   Handling basic exceptions and interrupts.
    *   ARM architected timer setup.
*   **Phase A3 (Context Switching & System Calls):**
    *   Implement task switching for AArch64.
    *   Low-level system call entry/exit (`svc` instruction).
*   **Phase A4 (Advanced Features):**
    *   SMP support (PSCI for CPU on/off, spin-tables).
    *   Further device support based on common ARM platforms (e.g., Raspberry Pi, QEMU virt machine).

## Build System Integration

*   The build system will use Rust's target triples (e.g., `x86_64-unknown-none`, `aarch64-unknown-none-softfloat`).
*   `Cargo.toml` will define features to conditionally compile code for different architectures or specific capabilities within an architecture.
*   `build.rs` might be used to:
    *   Select architecture-specific linker scripts.
    *   Pass architecture-specific flags to the compiler.
    *   Generate configuration based on the target architecture.
*   The `.cargo/config.toml` file will specify the default target, but this can be overridden for cross-compilation.

## Integration Points

*   **Memory Management (`src/mm`):** Will rely heavily on `arch` for MMU configuration, page table formats, and cache management.
*   **Kernel Core (`src/lib.rs`):** For invoking early architecture-specific initialization.
*   **Interrupts Module (`src/interrupts`):** Will use the `InterruptController` trait provided by `arch`.
*   **Drivers (`src/drivers`):** For platform-specific device interaction and interrupt handling.
*   **Initialization (`src/init`):** For coordinating architecture-specific setup routines during boot.

This plan provides a roadmap for developing a kernel that is portable across x86_64 and ARM64 architectures.
