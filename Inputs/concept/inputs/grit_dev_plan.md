# GritOS Development Plan

## Overview

This document outlines the development plan for GritOS, a new kernel written in Rust. It aims to replicate core functionalities found in a Linux-like kernel, starting with the logic equivalent to `init/main.c`. The project draws inspiration from standard Linux kernel design and aims for idiomatic Rust implementation.

## Guiding Principles

*   **Safety:** Leverage Rust's safety features to build a robust kernel.
*   **Modularity:** Develop components in a modular fashion.
*   **Idiomatic Rust:** Prefer Rust best practices and design patterns.
*   **Simplicity:** Aim for a simplified structure compared to large monolithic kernels where feasible.

## Core Modules & Development Plans

The kernel is organized into several core modules, located under the `src/` directory. Each module has its own detailed development plan:

*   [Architecture Specifics (`arch`)](./src/arch/arch_dev_plan.md)
*   [Memory Management (`mm`)](./src/mm/mm_dev_plan.md)
*   [File Systems (`fs`)](./src/fs/fs_dev_plan.md)
*   [Device Drivers (`drivers`)](./src/drivers/drivers_dev_plan.md)
*   [Initialization (`init`)](./src/init/init_dev_plan.md)
*   [Interrupt Handling (`interrupts`)](./src/interrupts/interrupt_dev_plan.md)
*   [Kernel Core (`src/main.rs`)](./src/main.rs) - *Initial entry and early boot sequence.*

## Development Phases (High Level)

This section outlines the overall development trajectory. Detailed sub-plans are linked within each phase or module description.

### 0. Project Philosophy & Scope Definition (Foundation)

*   **Goal:** Create a new kernel in Rust that replicates the functionality of `deepm1nd/linux`, starting from the logic equivalent to `init/main.c`. This project is envisioned as "grit-os".
*   **Strategy:** Incremental rewrite and development, component by component, building upon a foundational Rust kernel base.
*   **Rust Advantages:** Leverage Rust's memory safety, concurrency features, and modern tooling to build a more robust and maintainable kernel.
*   **Target Architectures:** Initially x86_64, with planned support for ARM64 (AArch64).
*   **Definition of "Done" for Phase 1:** Kernel boots, initializes core subsystems (memory, basic scheduler), and successfully runs the first Rust-based kernel thread to completion or enters an idle loop.
*   **Approach to C code:** All new functional code will be in Rust. Identify C libraries, data structures, and common patterns in `deepm1nd/linux` and develop idiomatic Rust equivalents and utility crates.

## Multi-Architecture Strategy

Supporting multiple architectures (x86_64 and ARM64) is a key goal for GritOS. This will be achieved through:

*   **Abstraction of Architecture-Specifics:** Identifying and isolating architecture-dependent code (e.g., context switching, MMU manipulation, interrupt control, boot procedures) within the `src/arch/` module. Common kernel subsystems will interact with the `arch` module through defined traits or APIs.
*   **Conditional Compilation:** Extensive use of Rust's conditional compilation features (e.g., `#[cfg(target_arch = "x86_64")]`, `#[cfg(target_arch = "aarch64")]`) to include or exclude architecture-specific code paths.
*   **Directory Structure:** Architecture-specific modules will reside in subdirectories within `src/arch/`, such as `src/arch/x86_64/` and `src/arch/aarch64/`. Common `arch` code or traits might live in `src/arch/common/` or directly in `src/arch/mod.rs`.
*   **Build System Integration:** The build system (Cargo features, `build.rs`) will manage the compilation process for different target architectures, ensuring correct source files and configurations are used. This will involve specific target triples (e.g., `x86_64-unknown-none`, `aarch64-unknown-none-softfloat`).
*   **Phased Implementation:** Support for ARM64 will be developed incrementally after establishing a stable foundation on x86_64.

### 1. Phase 1: Core Kernel Boot and Initialization (Reaching a Rust `start_kernel` equivalent)
This phase focuses on getting the kernel to a basic operational state, capable of running its first Rust code and initializing fundamental hardware and software services. This includes:
    *   Project initialization, directory structure (standard Rust layout: `src/lib.rs` and submodules).
    *   Basic bootloader integration (conceptual, via `bootimage` or similar).
    *   Kernel entry point (`_start`).
    *   Minimal console output and panic handler.
*   See detailed plan in [./src/init/init_dev_plan.md](./src/init/init_dev_plan.md).

### 2. Phase 2: Reaching `rest_init` Equivalent and Spawning First "Real" Kernel Process/Task
This phase builds upon the initialized kernel to establish basic process management and prepare for userspace.
*   **2.1. Process Management Core:**
    *   Refine `Task` structure in Rust with fields for parent/child relationships, credentials (stubs for now), signal handlers (stubs).
    *   Implement robust PID allocation.
    *   Develop APIs for creating and managing kernel threads.
    *   Implement basic process states (Running, Ready, Blocked, Zombie).
*   **2.2. The `init` Task (PID 1 Equivalent for Kernel):**
    *   The first significant kernel thread created by the scheduler.
    *   This thread will continue the kernel initialization process, similar to Linux's `kernel_init` running as PID 1.
    *   It will be responsible for initializing further subsystems that require a process context.
*   **2.3. Synchronization Primitives:**
    *   Develop robust spinlocks, mutexes, semaphores, and condition variables in Rust.
    *   Ensure they are suitable for `no_std` kernel environment and consider interrupt safety.
*   **2.4. System Call Interface (Basic Infrastructure):**
    *   Implement the mechanism for system call dispatch (e.g., using `SYSCALL`/`SYSRET` or software interrupt).
    *   Set up a system call table.
    *   No actual user-facing syscalls implemented yet, but the kernel can handle the entry/exit path.
*   **2.5. VFS (Virtual File System) Stubs & Early Mounts:**
    *   Define core VFS traits and structures in Rust (e.g., `File`, `Inode`, `Filesystem`, `Mount`).
    *   Implement a dummy root filesystem or a very simple ram-based filesystem (`ramfs`).
    *   Mount the initial root filesystem.
*   **2.6. Work Queues / Tasklets:**
    *   Implement a basic work queue system for deferring work in the kernel.

### 3. Phase 3: Basic Userspace Support & Core Device Drivers
The focus here is to enable the kernel to run the first userspace programs and interact with basic hardware.
*   **3.1. ELF Loader (Statically Linked Binaries):**
    *   Implement Rust code to parse and load statically linked ELF64 binaries.
    *   Set up user mode segments, stack, and instruction pointer.
    *   Allocate and map memory for the user process.
*   **3.2. Transition to Userspace:**
    *   Implement the architecture-specific code to switch from kernel mode to user mode to run the loaded ELF binary.
*   **3.3. System Call Implementation (Minimal Set for a Shell):**
    *   `exit`, `exit_group`
    *   `read`, `write` (to console/serial)
    *   `open`, `close`, `stat` (for basic VFS interaction)
    *   `brk` (or `mmap` for anonymous memory) for heap management in user programs.
    *   `fork` (or a Rust-idiomatic spawn mechanism for new processes)
    *   `execve` (to load and run new programs)
    *   `waitpid` (or equivalent)
    *   `getpid`, `getppid`
*   **3.4. Device Driver Model & Early Drivers:**
    *   Establish a basic device model (e.g., device registration, driver binding).
    *   PCI Bus Enumeration (basic).
    *   Fully implemented keyboard driver (PS/2 or USB HID if ambitious, via I/O APIC).
    *   Real-time Clock (RTC) driver for time.
*   **3.5. Filesystems (Basic User-Accessible):**
    *   `devtmpfs`: Create device nodes in `/dev` (e.g., `/dev/ttyS0`, `/dev/kbd`).
    *   `ramfs`/`tmpfs`: For general-purpose in-memory storage.
    *   Initial `procfs` (very basic, e.g., `/proc/self`, `/proc/cpuinfo` stub).
*   **3.6. Root Filesystem and `init` Process:**
    *   Mount a ramdisk containing a simple statically linked Rust `init` program (and a basic shell).
    *   The kernel's PID 1 (`kernel_init` equivalent) should execute this userspace `init` program.

### 4. Subsequent Phases (Very High-Level Roadmap)
These phases represent longer-term goals and will be detailed further as the project progresses.
*   **4.1. Full VFS & Major Filesystems:** (e.g., ext2, FAT32)
*   **4.2. Networking Stack:** (TCP/IP)
*   **4.3. Advanced Process & Memory Management:** (Paging to disk, shared memory, signals)
*   **4.4. Broader Hardware Support & Drivers:** (USB, storage controllers, graphics)
*   **4.5. Security Frameworks:** (Permissions, capabilities)
*   **4.6. Kernel Module System (Dynamic Loading):**

### 5. C Library Equivalents & Kernel Internals in Rust
This is an ongoing effort throughout all phases, focusing on replacing C idioms with robust Rust alternatives.
*   **Data Structures:** Efficient and safe implementations of lists, queues, maps, etc.
*   **Synchronization Primitives:** As detailed in Phase 2.
*   **Bit Operations:** Utilities for common bit manipulation tasks.
*   **String and Memory Operations:** Safe wrappers and functions for memory manipulation.
*   **Error Handling:** Consistent use of `Result` and custom error types.
*   **`no_std` Ecosystem:** Leveraging and contributing to the `no_std` Rust ecosystem.
*   **Atomic Operations:** Using Rust's atomic types for concurrent data access.
*   **Interfacing with Assembly:** For low-level hardware interaction, carefully managed.

## Build and Testing

*   Build System: `cargo` with custom build scripts (`build.rs`) and target specifications. Potentially a `grit_build_config_plan.md` for more advanced configuration.
*   Testing: QEMU for emulation, unit tests for suitable modules, integration tests for kernel subsystems.
```
