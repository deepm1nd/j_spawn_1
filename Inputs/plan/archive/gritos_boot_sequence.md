# gRiTOS Boot Sequence

## 1. Introduction and Guiding Principles

The gRiTOS boot sequence is engineered from the ground up with security, functional safety, and reliability as its paramount concerns. It is designed to establish a robust foundation for a Rust-based, microkernel operating system, ensuring that the system initializes into a known, safe, and secure state.

### 1.1 Core Tenets

**Security-First from Power-On:** Security is integral, not an add-on. The boot sequence must initiate by establishing or verifying a Hardware Root of Trust (HRoT). Each subsequent stage is responsible for verifying the integrity and authenticity of the next, maintaining a continuous chain of trust. This aligns with requirements such as `GRITOS-REQ-CS-00005` (secure boot chain), `GRITOS-REQ-CS-00006` (HRoT integration), and `GRITOS-REQ-CS-00009` (cryptographic verification of bootloader and kernel).

**Functional Safety and Predictability:** The primary goal is to bring the system into a well-defined, safe operational state (`GRITOS-REQ-FS-00008`). Early boot stages must incorporate mechanisms for fault detection, and where possible, recovery or transition to a safe state. The sequence aims for deterministic behavior to support real-time requirements (`GRITOS-REQ-RT-00001`, `GRITOS-REQ-RT-00005`).

**Rust-Centric Implementation:** In accordance with `GRITOS-REQ-GEN-00001`, the boot components beyond initial, unavoidable hardware-specific assembly or C (e.g., ROM bootloader, early UEFI phases) will be implemented in Rust. This leverages Rust's memory safety and type safety features as early as feasible to minimize vulnerabilities.

**Microkernel Architecture Support:** The boot sequence is tailored to initialize a minimal microkernel. Its primary responsibilities include setting up the kernel's execution environment and preparing to launch isolated user-space processes for system services and device drivers, reflecting the principles in Development Plan section 2.2.

**Modularity, Portability, and Verifiability:** The boot process is divided into distinct, well-defined stages. This modularity enhances clarity, testability, and simplifies porting to different hardware architectures (`GRITOS-REQ-GEN-00012`). Each stage will have clear responsibilities. The design aims for minimality in critical boot components to reduce the Trusted Computing Base (TCB) (`GRITOS-REQ-CS-00016`) and facilitate verification efforts, including formal verification (`GRITOS-REQ-FS-00010`, `GRITOS-REQ-FS-00011`), drawing inspiration from seL4's verified boot process (`GRITOS-REQ-FS-00009`).

**Explicit Resource Management:** Following principles like those in seL4, the boot sequence will emphasize explicit accounting and management of hardware resources from the earliest stages.

**Resilience and Recovery:** The boot process will be designed to be resilient to certain types of failures, providing clear diagnostics and, where feasible, mechanisms for recovery or falling back to a minimal safe mode.

This philosophy ensures that gRiTOS starts every time in a state that is not only operational but also trustworthy, forming a reliable bedrock for all subsequent operations.

### 1.2 Document Scope and Purpose
This document specifies the sequential stages and critical operations involved in booting the gRiTOS operating system, from hardware power-on to the initialization of the first user-space process and subsequent system services. It details the expected responsibilities, data flow, security considerations, and implementation guidelines for each stage. The purpose is to provide a clear and actionable reference for developers implementing or interfacing with the gRiTOS boot process, ensuring alignment with the core principles of security, safety, and reliability.

## 2. Boot Process Overview

The gRiTOS boot process is divided into several distinct stages, each with specific responsibilities. This staged approach ensures a modular, verifiable, and secure startup sequence. The transition between stages involves explicit handoffs of control and, where applicable, verification of the next stage's integrity.

2.1. **Stage 0: Pre-Boot Initialization (Hardware/Firmware)**
    *   *Responsibility:* Platform hardware and vendor-supplied firmware (e.g., BIOS, UEFI, SoC Boot ROM).
    *   *Outcome:* Basic hardware initialization (CPU cores, memory controller, minimal peripherals) and identification of a boot device. Control is transferred to a known entry point for Stage 1.
2.2. **Stage 1: Bootloader Execution**
    *   *Responsibility:* Primary or secondary bootloader (e.g., GRUB, U-Boot, or a custom gRiTOS bootloader).
    *   *Outcome:* Locates the gRiTOS kernel image on the boot device, potentially performs initial integrity checks, loads the kernel into memory, gathers essential hardware information (memory map, device tree if applicable), and transfers execution to the kernel entry point.
2.3. **Stage 2: Kernel Loading, Verification, and Entry**
    *   *Responsibility:* The initial phase of the gRiTOS kernel itself or a small, trusted loader accompanying it.
    *   *Outcome:* The kernel image is fully loaded and its integrity and authenticity are cryptographically verified. The execution environment is prepared for the Rust-based kernel code (e.g., setting up a temporary stack, CPU mode transition). Control is transferred to the main Rust kernel entry point (`kmain` or equivalent).
2.4. **Stage 3: Early Kernel Initialization (Rust `no_std` Environment)**
    *   *Responsibility:* The first Rust code executed in the kernel. Operates in a `no_std` environment.
    *   *Outcome:* Initializes essential early kernel structures. This includes setting up basic serial console output for debugging, processing bootloader-provided information, initializing a basic memory allocator for early kernel needs (e.g., a bump allocator for critical structures), and preparing for HAL initialization.
2.5. **Stage 4: Core Kernel Subsystems Initialization**
    *   *Responsibility:* Kernel code.
    *   *Outcome:* Initializes critical kernel subsystems. This includes:
        *   Hardware Abstraction Layer (HAL) initialization for platform-specific components.
        *   Full Memory Management Unit (MMU/MPU) setup (page tables, memory protection).
        *   Interrupt handling mechanisms (Interrupt Controller, vector tables).
        *   Scheduler and basic process/thread management structures.
        *   Timer and clock initialization.
        *   Inter-Process Communication (IPC) primitives.
2.6. **Stage 5: First User-Space Process Launch**
    *   *Responsibility:* Kernel.
    *   *Outcome:* The kernel creates and launches the first user-space process (often an "init" process or a "service manager"). This process is granted minimal necessary capabilities and runs in a restricted environment. This marks the transition from kernel-only mode to a system capable of running user-level code.
2.7. **Stage 6: System Services and Application Initialization**
    *   *Responsibility:* The first user-space process and subsequent processes it spawns.
    *   *Outcome:* Essential system services and device drivers (if running in user-space as per microkernel design) are started. Filesystems are mounted. The system reaches its fully operational state, ready to execute applications.

These stages provide a clear progression from hardware power-on to a fully functional gRiTOS environment, with security and safety checks integrated at critical transition points.

### 2.8 Summary of Control and Data Flow
The gRiTOS boot process follows a sequential transfer of control, where each stage, upon completion and verification (where applicable), loads and jumps to the entry point of the subsequent stage. Key data, such as the hardware memory map, device tree information, and kernel command line, is progressively gathered or passed from the firmware through the bootloader to the kernel. The kernel then processes this information to configure its subsystems and prepare the environment for user-space operations. Specific data structures like ArchBootInfo and KernelConfig (detailed later) are crucial for this handoff.

## 3. Detailed Boot Stage Elaboration

This section elaborates on each of the high-level boot stages, providing details on their purpose, key activities, security considerations, and relevant technical notes.

### 3.1 Stage 0: Pre-Boot Initialization (Hardware/Firmware)

#### 3.1.1 Purpose
*   To perform fundamental hardware initialization required before any OS-level bootloader can execute.
*   To select a boot device and load the initial bootloader code (Stage 1) from it.
*   This stage is entirely executed by code residing in the system's Boot ROM, SPI flash, or equivalent non-volatile storage, provided by the hardware vendor (e.g., BIOS, UEFI firmware, SoC's internal Boot ROM).

#### 3.1.2 Key Activities & Data Structures
*   **Power-On Self-Test (POST):** Basic checks of CPU, memory, and essential peripherals.
*   **CPU Initialization:** Initializes CPU cores to a known state, potentially sets up initial cache configurations. On multi-core systems, one core is typically designated as the primary boot core.
*   **Memory Controller Initialization:** Configures DRAM controllers and makes system RAM accessible.
*   **Bus Initialization:** Initializes essential system buses (e.g., PCIe, I2C, SPI) to allow access to critical peripherals.
*   **Boot Device Selection:** Determines the boot device based on firmware settings (e.g., boot order in BIOS/UEFI) or hardware straps.
*   **Loading Stage 1 Bootloader:** Reads the first sector(s) of the selected boot device (e.g., Master Boot Record - MBR, or EFI System Partition - ESP for UEFI) into RAM.
*   **Transfer of Execution:** Jumps to the entry point of the loaded Stage 1 bootloader.

#### 3.1.3 Data Handoff to Bootloader
##### 3.1.3.1 Finalized Memory Map
A comprehensive description of all usable RAM, reserved regions, memory-mapped I/O, and firmware-specific areas.
##### 3.1.3.2 Pointer to UEFI System Table (if UEFI boot)
This table is the entry point for all UEFI boot and runtime services.
##### 3.1.3.3 Pointer to the final ACPI tables (e.g., RSDP pointer)
Allows the OS to find and parse ACPI tables for hardware configuration, power management, etc.
##### 3.1.3.4 Pointer to the Device Tree Blob (DTB) (especially for ARM/RISC-V)
Describes the hardware layout to the OS if ACPI is not used or is insufficient.
##### 3.1.3.5 Boot arguments or command line
Any parameters provided by the firmware or a boot manager that selected the Stage 1 bootloader.
##### 3.1.3.6 (UEFI specific) Handles to loaded image, boot device, etc.

#### 3.1.4 Security Considerations
*   **Hardware Root of Trust (HRoT):** This stage is the earliest point where an HRoT can be established or leveraged.
    *   **Secure Boot (e.g., UEFI Secure Boot, vendor-specific Secure Boot):** The firmware may verify the cryptographic signature of the Stage 1 bootloader against trusted certificates stored in firmware. This is critical for `GRITOS-REQ-CS-00005` (secure boot chain) and `GRITOS-REQ-CS-00006` (HRoT integration).
    *   **Trusted Platform Module (TPM):** If available, the firmware might measure the Stage 1 bootloader into TPM Platform Configuration Registers (PCRs) to record its integrity.
*   **Firmware Integrity:** The security of the entire system relies on the integrity of this firmware. Compromised firmware can undermine all subsequent security measures. Firmware updates themselves must be secure.
*   **Configuration Security:** Firmware settings (e.g., boot order, Secure Boot keys) must be protected against unauthorized modification (e.g., via firmware password).

#### 3.1.5 Implementation Notes
*   Not applicable for this stage, as it precedes any gRiTOS or Rust code. gRiTOS relies on the hardware/firmware to correctly execute this stage.

#### 3.1.6 Cross-Kernel Comparisons/Inspirations
*   **All Kernels:** All operating systems rely on this initial hardware-controlled phase. The level of sophistication varies greatly (e.g., simple BIOS POST vs. complex UEFI environment).
*   **seL4:** Emphasizes minimizing trust in firmware. The formally verified boot process of seL4 often involves a very small, trusted loader that takes over quickly from the platform firmware.
*   **Zircon (Fuchsia):** Supports various bootloaders like Gigaboot (UEFI-based) which handle interactions with the firmware environment.
*   **Redox OS:** Details boot processes for BIOS and UEFI, highlighting the firmware's role in loading the initial stages of the Redox bootloader.

#### 3.1.7 gRiTOS Expectations for Firmware
*   gRiTOS expects this stage to correctly initialize essential hardware and securely load and transfer control to a verifiable Stage 1 bootloader.
*   For systems requiring high assurance, the platform must support a strong HRoT and a secure boot mechanism verifiable by the gRiTOS bootloader or early kernel code.

#### 3.1.8 Conceptual Firmware Interactions (Post-Boot)
*   While Stage 0 is complete once the Stage 1 bootloader is running, gRiTOS may later interact with services or data provided or maintained by the platform firmware. These are not part of the boot sequence itself but rely on firmware setup. Examples include:
    *   **UEFI Runtime Services:** For tasks like getting/setting time, variable services (if permitted by security policy). Conceptual module: `kernel::platform::uefi_runtime`.
    *   **ACPI Control Methods:** Executing ACPI AML code for power management or hardware control. Conceptual module: `kernel::platform::acpi_interpreter`.
    *   **TPM (Trusted Platform Module) Driver:** Interacting with a TPM for measurements, sealing, or attestation, relying on firmware to have initialized the TPM. Conceptual module: `kernel::drivers::security::tpm`.
    *   **System Management Mode (SMM) Services:** On x86, some hardware control or monitoring might occur via SMM, which is largely opaque to the OS but set up by firmware.

### 3.2 Stage 1: Bootloader Execution

#### 3.2.1 Purpose
*   To bridge the gap between basic hardware initialization (Stage 0) and the execution of the gRiTOS kernel.
*   To locate the gRiTOS kernel image and other necessary components (e.g., initial RAM disk, configuration files) on a storage device.
*   To load the kernel into RAM, provide it with essential boot information, and transfer execution control to it.
*   This stage can be handled by industry-standard bootloaders (e.g., GRUB2, U-Boot, UEFI boot managers) or, for specific high-assurance scenarios, a custom gRiTOS bootloader component.

#### 3.2.2 Key Activities & Data Structures
The bootloader initializes its own execution environment. UEFI bootloaders have access to UEFI Boot Services. Simpler bootloaders (like those from MBR) operate with minimal services. It contains or loads drivers for supported filesystems (e.g., FAT32 for ESP, ext2/4, or a gRiTOS-specific one if custom) and storage controllers (e.g., AHCI for SATA, NVMe). It reads configuration files (e.g., `grub.cfg`, `boot.scr` for U-Boot, or a gRiTOS-defined format) to determine kernel location, kernel command-line parameters, and paths to other required files. Data Flow: Bootloader reads `gritos_boot.cfg` (example name) -> parses into an internal `BootloaderConfig` struct. The bootloader then reads the gRiTOS kernel image from the storage device into a suitable location in RAM. Data Flow: `BootloaderConfig` provides kernel path -> `gritos_boot::fs` reads kernel ELF bytes into RAM buffer. If used, an initrd image is loaded into memory; this image might contain early user-space programs or drivers needed before the main root filesystem is accessible.

##### 3.2.2.1 Hardware Information Aggregation
Collects critical hardware information to pass to the kernel. This includes:
*   **Memory Map:** Detailed map of usable RAM, reserved regions, firmware-allocated areas. (Crucial for `GRITOS-REQ-GEN-00005`, `GRITOS-REQ-GEN-00009`).
*   **Device Tree Blob (DTB):** On ARM/RISC-V platforms, a DTB is often passed to describe non-discoverable hardware.
*   **ACPI Tables:** Provided by firmware, made accessible by the bootloader.
*   **Kernel Command Line:** A string of parameters that can alter kernel behavior (e.g., debug flags, root partition identifier, init program path). Zircon's extensive use of command-line options (Zircon Boot Process Ref) is a good example.
Data Flow: Platform-specific services (e.g., UEFI Boot Services, direct hardware queries) populate fields in the `ArchBootInfo` structure.

##### 3.2.2.2 Kernel Entry Preparation
Ensures the CPU is in the correct mode (e.g., 64-bit long mode for x86-64, appropriate EL for ARM). May set up a preliminary page table or identity mapping for the kernel. Places pointers to boot information (e.g., memory map, DTB pointer, command line) in defined registers or memory locations as per the kernel's expected ABI.

##### 3.2.2.3 Transfer of Execution to Kernel
Jumps to the kernel's entry point.

#### 3.2.3 Security Considerations
*   **Chain of Trust Continuation (`GRITOS-REQ-CS-00005`):**
    *   If Stage 0 (Firmware Secure Boot) verified this bootloader, this bootloader is now responsible for verifying the gRiTOS kernel (Stage 2) before loading and executing it. This involves checking cryptographic signatures against trusted keys.
    *   gRiTOS might require its bootloader (or the configuration of a standard one) to enforce a strict policy where only signed kernels are booted.
*   **Configuration Integrity:** Bootloader configuration files must be protected from tampering. Unauthorized modification could lead to loading a malicious kernel or unsafe parameters. (Consider read-only partitions or signed configuration updates).
*   **Minimizing Attack Surface:** The bootloader itself should be as simple as possible if custom-developed, or a well-vetted standard bootloader should be used. Complex bootloaders with many features (network stack, extensive scripting) can introduce vulnerabilities.
*   **Information Disclosure:** The bootloader should avoid unnecessarily exposing sensitive information via its UI or logs.
*   **Measured Boot:** If a TPM is used, the bootloader should measure the kernel image and its configuration into TPM PCRs before execution.

#### 3.2.7 Implementation Notes & Code Examples

##### 3.2.7.1 Custom Bootloader Components in Rust
As per `planning/gritOS Development Plan.md` (Section 3.1), parts of a custom bootloader could be developed in Rust, especially for logic after initial hardware setup (which might still need assembly/C). A Rust-based bootloader component could handle filesystem parsing, kernel image loading, and verification using Rust crypto libraries. This aligns with `GRITOS-REQ-GEN-00001` (all components in Rust where feasible).

##### 3.2.7.2 Proposed Bootloader Modules (`gritos_boot` crate)
```rust
// Conceptual modules for a Rust-based gRiTOS Bootloader (crate: `gritos_boot`)
// pub mod main;             // Main bootloader logic, entry point
// pub mod config;           // Parses bootloader configuration (e.g., gritos_boot.cfg)
// pub mod fs;               // Filesystem drivers (e.g., FAT32, minimal ext2)
// pub mod elf_loader;       // Basic ELF parsing to find kernel size/load reqs (not full load)
// pub mod verifier;         // Signature verification for the kernel
// pub mod hw_info;          // Gathers hardware information (memory map, etc.)
// pub mod platform;         // Platform-specific code (e.g., UEFI services access, BIOS calls)
// pub mod early_console;    // For printing bootloader messages
```

##### 3.2.7.3 Conceptual `ArchBootInfo` Structure
```rust
// Conceptual ArchBootInfo structure prepared by the bootloader.
// This is the primary data structure passed to the gRiTOS kernel.
#[repr(C)] // To ensure a well-defined layout if passed from assembly.
pub struct ArchBootInfo {
    pub signature: [u8; 16],          // e.g., "GRITOS_BOOTINFO "
    pub version: u32,                 // Version of this structure
    pub bootloader_name: [u8; 32],    // Name of the bootloader
    pub bootloader_version: u32,      // Version of the bootloader

    pub kernel_image_addr: u64,       // Physical start address of the loaded kernel image
    pub kernel_image_size: u64,       // Size of the loaded kernel image in bytes
    
    pub initrd_addr: u64,             // Physical start address of the initrd (if any)
    pub initrd_size: u64,             // Size of the initrd in bytes

    pub memory_map_addr: u64,         // Physical address of the memory map entries
    pub memory_map_entries: u64,      // Number of memory map entries
    pub memory_map_entry_size: u64,   // Size of a single memory map entry
    // Note: Memory map entries themselves would define type, base, length, attributes.

    pub cmdline_addr: u64,            // Physical address of the null-terminated kernel command line string
    pub cmdline_size: u64,            // Length of the command line string (including null)

    pub acpi_rsdp_addr: u64,          // Physical address of ACPI RSDP table (if applicable)
    pub smbios_entry_addr: u64,       // Physical address of SMBIOS entry point (if applicable)

    pub dtb_addr: u64,                // Physical address of the Device Tree Blob (DTB) (if applicable)
    pub dtb_size: u64,                // Size of the DTB

    pub framebuffer_addr: u64,        // Physical address of the framebuffer
    pub framebuffer_width: u32,
    pub framebuffer_height: u32,
    pub framebuffer_pitch: u32,
    pub framebuffer_bpp: u8,
    // Add other framebuffer format details as needed

    // Add other platform specific details, e.g., pointers to UEFI tables if needed by kernel
}
```

##### 3.2.7.4 Conceptual Bootloader Logic (`prepare_and_jump_to_kernel`)
```rust
// Conceptual bootloader function (within `gritos_boot::main` or similar)
fn prepare_and_jump_to_kernel(platform_services: &dyn platform::PlatformServices) -> Result<!, BootError> {
    // 1. Initialize console
    let console = early_console::init(platform_services);
    console.write_line("gRiTOS Bootloader Initializing...");

    // 2. Load config
    // Data Flow: Disk -> config_data_buffer -> parsed_config
    let config_data = fs::read_file(platform_services, "/boot/gritos_boot.cfg")?;
    let parsed_config = config::parse_config(&config_data)?;
    console.write_line("Config loaded.");

    // 3. Load kernel image
    // Data Flow: Disk -> kernel_image_buffer
    let kernel_image_buffer = fs::read_file(platform_services, &parsed_config.kernel_path)?;
    console.write_line("Kernel image loaded.");

    // 4. Verify kernel signature (using `gritos_boot::verifier`)
    // Data Flow: kernel_image_buffer -> signature_check_ok
    let signature_path = parsed_config.kernel_path.clone() + ".sig"; // Example
    let signature_data = fs::read_file(platform_services, &signature_path)?;
    if !verifier::verify_kernel(&kernel_image_buffer, &signature_data, &parsed_config.trusted_pub_key_path)? {
        console.write_line("Kernel verification FAILED!");
        return Err(BootError::VerificationFailed);
    }
    console.write_line("Kernel signature verified.");

    // 5. Load initrd (if specified in config)
    let mut initrd_info = (0,0);
    if let Some(initrd_path) = &parsed_config.initrd_path {
        let initrd_buffer = fs::read_file(platform_services, initrd_path)?;
        // Store initrd_buffer somewhere safe, get address/size
        // initrd_info = (get_initrd_addr(&initrd_buffer), initrd_buffer.len() as u64);
        console.write_line("Initrd loaded.");
    }

    // 6. Gather hardware information (using `gritos_boot::hw_info`)
    // Data Flow: platform_services -> memory_map, dtb_ptr, etc. -> arch_boot_info
    let mut arch_boot_info = ArchBootInfo { /* zeroed or default */ };
    arch_boot_info.signature = *b"GRITOS_BOOTINFO ";
    arch_boot_info.version = 1;
    // ... populate bootloader_name, version ...
    // ... populate kernel_image_addr (where it was loaded), kernel_image_size ...
    // ... populate initrd_addr, initrd_size from initrd_info ...
    hw_info::populate_memory_map(platform_services, &mut arch_boot_info)?;
    hw_info::populate_cmdline(platform_services, &parsed_config.kernel_cmdline, &mut arch_boot_info)?;
    hw_info::populate_dtb_info(platform_services, &mut arch_boot_info)?;
    hw_info::populate_framebuffer_info(platform_services, &mut arch_boot_info)?;
    // ... and so on for ACPI, SMBIOS etc.
    console.write_line("Hardware information gathered.");

    // 7. Place kernel image at final destination, prepare ArchBootInfo at a known address
    let kernel_entry_point_addr = platform_services.prepare_kernel_environment(&kernel_image_buffer, &arch_boot_info)?;
    console.write_line("Kernel environment prepared. Jumping to kernel...");
    
    // 8. Jump to kernel entry point
    platform_services.jump_to_kernel(kernel_entry_point_addr, get_arch_boot_info_final_addr());
    // This function should not return.
}
```

#### 3.2.8 Cross-Kernel Comparisons/Inspirations
*   **Redox OS:** Has its own bootloader written in Rust (Stage 3 for BIOS), demonstrating feasibility. Its boot process (Redox Boot Process Ref) clearly defines how it locates and loads the kernel.
*   **Zircon (Fuchsia):** Uses Gigaboot (UEFI) or Zirconboot. The emphasis on passing a kernel command line for configuration is noteworthy (Zircon Kernel Command Line Options Ref).
*   **seL4:** Often uses minimal, highly trusted bootloaders. The focus is on getting to the verified kernel code as quickly and securely as possible.
*   **Linux:** GRUB, U-Boot are common. They are highly configurable and feature-rich, offering flexibility but also a larger TCB. The `initrd` concept is widely used.

#### 3.2.9 gRiTOS Requirements for Bootloader
*   The bootloader must reliably load the gRiTOS kernel and provide accurate hardware information.
*   It plays a crucial role in the secure boot chain (`GRITOS-REQ-CS-00005`, `GRITOS-REQ-CS-00009`).
*   Kernel command-line parameters passed by the bootloader will be a key configuration mechanism.
*   If a standard bootloader is used, its configuration must be carefully managed and secured.

### 3.3 Stage 2: Kernel Loading, Verification, and Entry

#### 3.3.1 Purpose
*   To ensure the gRiTOS kernel image is correctly placed in memory by the bootloader (Stage 1).
*   To perform rigorous cryptographic verification of the kernel's integrity and authenticity before execution.
*   To set up the minimal CPU environment required for the Rust-based high-level kernel code to start (e.g., stack, CPU mode).
*   To transfer control to the main Rust kernel entry point (often called `kmain`, `rust_main`, or similar).

#### 3.3.2 Key Activities & Data Structures

##### 3.3.2.1 Kernel Image Placement
The bootloader (Stage 1) is assumed to have loaded the kernel ELF (or similar format) image into a known memory location. This location might be specified by the kernel image itself or determined by the bootloader.

##### 3.3.2.2 Verification (Critical Security Step - `GRITOS-REQ-CS-00009`)
This step might be performed by a small, trusted loader component that is part of the gRiTOS kernel binary itself (if the main bootloader is untrusted or its verification capabilities are insufficient), or by the Stage 1 bootloader if it's trusted and capable. The signature of the loaded kernel image is checked against a known, trusted public key. This key might be embedded in the loader component or provided by a secure element/firmware. If verification fails, the boot process must halt or enter a safe recovery mode. **Booting an unverified kernel is a critical security failure.**
Data Flow: If verification is done here, a `kernel::boot::verifier` module would read the kernel image bytes (already in RAM) and check its signature against an embedded public key. The `ArchBootInfo` pointer is received from the bootloader (e.g., via a specific register as per ABI).

##### 3.3.2.3 CPU Context Setup (Architecture-Specific)
*   **x86-64:** Ensure the CPU is in 64-bit long mode, basic paging might be set up by the bootloader (e.g., identity mapping the kernel). A stack pointer (`RSP`) must be initialized. Segmentation should be set to a flat model.
*   **AArch64:** Ensure the CPU is in EL1 (for a type 1 hypervisor/OS) or EL2 (if gRiTOS is a hypervisor). A stack pointer (`SP_EL1` or `SP_EL0` if transitioning to EL0 immediately) must be set up. MMU and caches might be in a basic state set by the bootloader.
*   **RISC-V:** Ensure the CPU is in Supervisor mode. A stack pointer (`sp`) must be initialized.

##### 3.3.2.4 Boot Information Processing
The address or method to access bootloader-provided information (memory map, command line, DTB) is retrieved. This information might be passed in registers or via a predefined memory structure.

##### 3.3.2.5 Relocation (if necessary)
If the kernel is compiled to be position-independent, or loaded at an address different from its link address, relocations might need to be processed by an early part of the kernel loader.

##### 3.3.2.6 BSS Section Clearing
The BSS (Block Started by Symbol) section of the kernel image, which contains uninitialized static data, must be zeroed out.

##### 3.3.2.7 Initial Stack Setup
A temporary stack is set up for the very early Rust code. This stack might be statically allocated within the kernel image or placed in a known safe memory region.

##### 3.3.2.8 Transfer to Rust Entry Point
A final jump or call is made to the main Rust kernel function (e.g., `kmain(boot_info_ptr: *const BootInfo)`). Control Flow: An architecture-specific assembly stub (`kernel::arch::[target_arch]::entry::_start`) receives control, sets up the initial stack and CPU state (e.g., long mode, supervisor mode), places the `ArchBootInfo` pointer into the first argument register, and then calls `kernel::kmain`.

#### 3.3.3 Data Structures

##### 3.3.3.1 Kernel Image Format
Kernel Image (e.g., ELF file): Contains kernel code, data, BSS, relocation information.

##### 3.3.3.2 `ArchBootInfo` Reception
A pointer to this structure (defined by gRiTOS, populated by Stage 1) is the primary input to the Rust kernel entry point. It encapsulates the memory map, command line, DTB location, etc.
```rust
// Conceptual BootInfo structure passed to kmain.
// This structure is prepared by the bootloader (Stage 1) and its physical address
// is passed to the kernel entry point (e.g., in a specific register).
// The kernel will then access it via this pointer.
#[repr(C)]
pub struct ArchBootInfo {
    // ... (keep existing fields from previous enhancement of Stage 1) ...
    pub signature: [u8; 16],
    pub version: u32,
    pub bootloader_name: [u8; 32],
    pub bootloader_version: u32,
    pub kernel_image_addr: u64,
    pub kernel_image_size: u64,
    pub initrd_addr: u64,
    pub initrd_size: u64,
    pub memory_map_addr: u64,
    pub memory_map_entries: u64,
    pub memory_map_entry_size: u64,
    pub cmdline_addr: u64,
    pub cmdline_size: u64,
    pub acpi_rsdp_addr: u64,
    pub smbios_entry_addr: u64,
    pub dtb_addr: u64,
    pub dtb_size: u64,
    pub framebuffer_addr: u64,
    pub framebuffer_width: u32,
    pub framebuffer_height: u32,
    pub framebuffer_pitch: u32,
    pub framebuffer_bpp: u8,
}
```

#### 3.3.4 Security Considerations
*   **Kernel Integrity Verification (`GRITOS-REQ-CS-00009`):** This is the most critical security function of this stage. Failure to verify, or flaws in verification logic, can lead to arbitrary code execution with kernel privileges.
*   **Trusted Public Keys:** The public keys used for kernel verification must be securely stored and managed. Hardcoding them in a small, verified loader is common for embedded systems.
*   **Protection of Boot Information:** The boot information passed from the bootloader must be treated as untrusted until validated by the kernel. For example, memory map entries should be sanity-checked.
*   **Timing Attacks on Verification:** While less common at this stage, cryptographic operations should ideally use constant-time algorithms if there's any concern about attackers influencing boot duration.
*   **Ensuring Correct CPU Mode and Privileges:** Critical for preventing execution of kernel code in an incorrect or less privileged state.

#### 3.3.6 Implementation Notes & Code Examples

##### 3.3.6.1 Proposed Kernel Modules
```rust
// Conceptual kernel modules involved in Stage 2:
// pub mod kernel::arch::[target_arch]::entry; // Contains _start assembly and early Rust setup
// pub mod kernel::boot::loader;              // Contains logic if kernel does self-relocation or parsing
// pub mod kernel::boot::verifier;            // If kernel verifies itself (e.g., if bootloader is untrusted)
// pub mod kernel::kmain;                     // The main Rust entry point function resides here or is called from here
```

##### 3.3.6.2 Conceptual Assembly Entry (`_start`)
```assembly
// Example assembly stub (architecture-dependent)
// _start:
//   // Setup stack pointer (e.g., load from a known location or use a linked symbol)
//   mov rsp, OFFSET kernel_stack_top 
//   // Zero BSS (if not done by loader)
//   call zero_bss
//   // Pass boot_info pointer (e.g., in rdi for x86-64 SysV ABI)
//   mov rdi, OFFSET boot_info_struct 
//   // Call the main Rust function
//   call kmain 
// hang:
//   hlt
//   jmp hang
```

##### 3.3.6.3 Conceptual `kmain` Entry Signature
The first Rust function (`kmain`) will be `extern "C"` and `#[no_mangle]` to have a predictable name for the assembly stub. It will operate in a `no_std` environment.
```rust
// In kernel::kmain.rs or similar
#![no_std]
#![no_main]

// Assuming ArchBootInfo is defined in a shared module or copied here.
// use crate::boot::ArchBootInfo; // Or appropriate path
use core::panic::PanicInfo;

#[no_mangle]
pub extern "C" fn kmain(arch_boot_info_ptr: *const ArchBootInfo) -> ! {
    // Stage 2 complete, `kmain` has been called by `kernel::arch::[target_arch]::entry::_start`.
    // arch_boot_info_ptr contains the physical address of the ArchBootInfo structure
    // prepared by the bootloader.
    
    // First action: Initialize early console for printouts.
    // This belongs to Stage 3.
    // unsafe { kernel::early_init::console::init_early_console(); }
    // early_println!("gRiTOS Kernel: kmain entered. ArchBootInfo at: {:p}", arch_boot_info_ptr);

    // Validate the pointer and the signature of ArchBootInfo itself if desired.
    // if arch_boot_info_ptr.is_null() {
    //     panic!("ArchBootInfo pointer is null!");
    // }
    // let boot_info_ref = unsafe { &*arch_boot_info_ptr };
    // if &boot_info_ref.signature != b"GRITOS_BOOTINFO " {
    //    panic!("Invalid ArchBootInfo signature!");
    // }
    // early_println!("ArchBootInfo signature validated.");

    // Further processing of boot_info and transition to Stage 3
    // kernel::early_init::process_boot_info(boot_info_ref);
    
    loop { /* Halt */ }
}

#[panic_handler]
fn panic(info: &PanicInfo) -> ! {
    // Potentially use an early_println here if console is up.
    // early_println!("KERNEL PANIC (Stage 2/3): {}", info);
    loop { /* Halt */ }
}
```
The verification logic, if part of a Rust-based loader component, would use Rust crypto crates.

#### 3.3.7 Cross-Kernel Comparisons/Inspirations
*   **Linux:** The kernel has an early assembly entry (`head.S`) that handles CPU setup, page table creation, and then calls `start_kernel()`. Verification is typically handled by the bootloader (GRUB/U-Boot) using GPG signatures.
*   **seL4:** Has a minimal initial assembly part, then C code. Verification is paramount and often integrated into its build and deployment process. The focus is on a provably correct transition.
*   **Redox OS:** The `kstart` (assembly) and `kmain` (Rust) functions in `kernel/src/arch/...` show a clear transition. Redox's bootloader handles loading.
*   **Zircon:** The ZBI (Zircon Boot Image) format includes the kernel and a ramdisk. The bootloader loads this and Zircon's early code takes over. Verification might be done by the bootloader (e.g., Gigaboot using UEFI Secure Boot).

#### 3.3.8 gRiTOS Requirements & Expectations
*   A robust and verifiable kernel loading and verification process is non-negotiable (`GRITOS-REQ-CS-00009`, `GRITOS-REQ-CS-00005`).
*   The transition to the Rust `kmain` function must be clean, providing all necessary boot information in a structured way.
*   Early error handling (e.g., if verification fails or boot info is corrupt) must lead to a safe state.

### 3.4 Stage 3: Early Kernel Initialization (Rust `no_std` Environment)

#### 3.4.1 Purpose
*   To perform the first set of initializations within the Rust kernel environment, operating without the standard library (`no_std`) and before full memory management or scheduling is available.
*   To process bootloader-provided information securely.
*   To set up minimal hardware interactions (like serial console for debugging).
*   To prepare the system for the initialization of more complex kernel subsystems (Stage 4).

#### 3.4.2 Key Activities & Data Structures

##### 3.4.2.1 Entry from Stage 2
Control is received by the Rust `kmain` function (or equivalent) from the assembly or early C loader. A pointer to the `ArchBootInfo` structure is typically the primary input.

##### 3.4.2.2 Panic Handler Setup
Ensure a basic panic handler is in place. Initially, this might just print a message to the serial console (if available) and halt the system. Module: `kernel::panic`.
```rust
// #[panic_handler]
// fn panic(info: &core::panic::PanicInfo) -> ! {
//     early_println!("KERNEL PANIC in Stage 3: {}", info);
//     // TODO: Implement more robust panic handling, e.g., error codes, reset
//     loop { /* hlt or similar */ }
// }
```

##### 3.4.2.3 Serial Console Initialization (Early Debugging)
Module: `kernel::early_init::console`.
*   Initialize a serial port (UART) for `early_println!` macros. This involves `unsafe` MMIO or port I/O operations. The specific UART (address, type) might be known via platform convention or from `ArchBootInfo`.
*   This is critical for debugging the boot process before more complex output systems are ready.

##### 3.4.2.4 Boot Information Processing & Validation (`ArchBootInfo`)
Module: `kernel::early_init::bootinfo`. Data Flow: The `kmain` function passes the `arch_boot_info_ptr` to `kernel::early_init::bootinfo::process()`. This module is responsible for:
1.  Validating the `ArchBootInfo` pointer and its contents (e.g., signature, version, checksum if present).
2.  Copying relevant data from `ArchBootInfo` into a new, kernel-internal `KernelConfig` structure. This structure will be more Rust-idiomatic (e.g., using slices for memory map, `&str` for command line).
3.  Performing sanity checks on critical data like the memory map (checking for overlaps, sufficient RAM for kernel).
4.  Parsing the kernel command line into a more structured format within `KernelConfig`.
5.  Making the validated `KernelConfig` available globally (e.g., via a static OnceCell or a dedicated accessor function).

##### 3.4.2.5 CPU-Specific Early Initialization
Module: `kernel::arch::[target_arch]::cpu::early_setup`.
*   Initialize CPU features that were not set by Stage 2 (e.g., specific control registers, floating-point unit state).
*   On multi-core systems, this stage might involve basic procedures to bring up Application Processors (APs) or prepare for their later activation, though full SMP setup is usually later.

##### 3.4.2.6 Global Descriptor Table (GDT) / Interrupt Descriptor Table (IDT) Setup (x86 specific)
*   Load a basic GDT.
*   Load a preliminary IDT with handlers for exceptions (e.g., divide by zero, page fault). Initially, these handlers might just print a message and halt. Full interrupt handling comes later.

##### 3.4.2.7 Basic Memory Allocator (Pre-MMU)
Module: `kernel::memory::bootstrap_alloc`. Data Flow: This allocator is initialized by `kernel::early_init::init_bootstrap_allocator()` using a designated memory region identified from the validated `KernelConfig.memory_map`.

##### 3.4.2.8 Prepare for HAL and MMU Setup
Identify memory regions for kernel code/data, initial page tables, and other critical structures based on the validated memory map.

#### 3.4.3 Data Structures

##### 3.4.3.1 `ArchBootInfo` Consumption
`ArchBootInfo`: Consumed and validated.
Internal kernel structures for parsed boot info.
Static structures for GDT/IDT (if applicable).
State for the early memory allocator.
Early `println` buffer (if used).

##### 3.4.3.2 `KernelConfig` Structure Definition
```rust
// Conceptual KernelConfig structure, populated by kernel::early_init::bootinfo
// from ArchBootInfo. This is a validated, kernel-internal representation.
pub struct MemoryRegion {
    pub base: u64,
    pub size: u64,
    pub region_type: MemoryRegionType, // e.g., Usable, Reserved, ACPI, KernelImage
    // pub attributes: MemoryRegionAttributes, // e.g., ReadOnly, Cacheable
}

pub enum MemoryRegionType {
    Usable,
    Reserved,
    AcpiReclaimable,
    AcpiNvs,
    KernelImage,
    BootloaderReserved,
    // ... other types
}

pub struct FramebufferInfo {
    pub addr: u64,
    pub width: u32,
    pub height: u32,
    pub pitch: u32,
    pub bpp: u8,
    // pub format: PixelFormat, // More detailed format info
}

pub struct KernelConfig {
    pub bootloader_name: String, // Using String if bootstrap_alloc is available
    pub bootloader_version: u32,
    
    pub kernel_image_base: u64,
    pub kernel_image_size: u64,
    
    pub initrd_base: Option<u64>,
    pub initrd_size: Option<u64>,
    
    pub memory_map: Vec<MemoryRegion, &'static BootstrapAllocator>, // Assuming a static allocator for Vec
    
    pub cmdline: String, // Parsed and owned command line
    // pub parsed_cmdline_args: HashMap<String, String, &'static BootstrapAllocator>, // Or similar

    pub acpi_rsdp: Option<u64>,
    pub smbios_entry: Option<u64>,
    pub dtb_address: Option<u64>,

    pub framebuffer: Option<FramebufferInfo>,
    // ... other processed info
}
```

#### 3.4.4 Data and Control Flow
The `kmain` function, upon receiving the `arch_boot_info_ptr`, first initializes the early console. It then passes this pointer to `kernel::early_init::bootinfo::process()` to validate and transform the bootloader-provided data into the `KernelConfig` structure. This `KernelConfig` is then used to initialize the bootstrap memory allocator. Subsequent early initialization steps, like CPU setup, use information from `KernelConfig` and the newly available bootstrap allocator for any minor dynamic allocations (e.g., for storing parsed command line arguments if not using a static buffer). Control flows sequentially through these initialization sub-steps within Stage 3.

#### 3.4.5 Security Considerations
*   **Boot Information Trust:** Treat all information from the bootloader (`ArchBootInfo`) as potentially untrusted until thoroughly validated. Corrupt or malicious boot info could lead to crashes or vulnerabilities.
*   **Input Validation:** Apply strict validation to the kernel command line parameters.
*   **Secure Parsing of DTB:** If using a DTB, parse it carefully, as malformed DTBs can be a source of vulnerabilities.
*   **Exception Handlers:** Ensure that even early exception handlers fail safely (e.g., halt the system) rather than allowing undefined behavior.
*   **Minimizing `unsafe` Code:** While `unsafe` is necessary for hardware interaction (MMIO, port I/O, special registers), it should be encapsulated and minimized. Justify every `unsafe` block (`GRITOS-REQ-FS-00012`).

#### 3.4.6 Implementation Notes & Code Examples

##### 3.4.6.1 Proposed Kernel Modules
```rust
// Conceptual kernel modules involved in Stage 3:
// pub mod kernel::early_init::console;       // Early serial console setup.
// pub mod kernel::early_init::bootinfo;      // Processes ArchBootInfo into KernelConfig.
// pub mod kernel::memory::bootstrap_alloc; // Early physical memory allocator (pre-PMM).
// pub mod kernel::arch::[target_arch]::cpu::early_setup; // Early CPU features.
// pub mod kernel::panic;                     // Panic handler implementation.
```

##### 3.4.6.2 Conceptual `kmain` Logic for Stage 3
```rust
// Conceptual entry point for Stage 3 processing, called from kmain
// Assume KERNEL_CONFIG is a static OnceCell<KernelConfig> or similar
// Assume BOOTSTRAP_ALLOCATOR is a static OnceCell<BootstrapAllocator>

// pub fn kmain(arch_boot_info_ptr: *const ArchBootInfo) -> ! {
//     unsafe { kernel::early_init::console::init_qemu_uart_early(); } // Example direct init
//     early_println!("gRiTOS Kernel: kmain. ArchBootInfo at: {:p}", arch_boot_info_ptr);

//     // Process boot information
//     let kernel_config = kernel::early_init::bootinfo::process_arch_boot_info(arch_boot_info_ptr)
//         .expect("Failed to process ArchBootInfo!");
//     // KERNEL_CONFIG.set(kernel_config).unwrap();
//     early_println!("KernelConfig populated. Cmdline: {}", kernel_config.cmdline);

//     // Initialize bootstrap allocator using a region from KernelConfig's memory map
//     let bootstrap_alloc_region = kernel_config.memory_map.iter()
//         .find(|r| r.region_type == MemoryRegionType::Usable && r.size >= MIN_BOOTSTRAP_HEAP_SIZE)
//         .expect("No suitable region for bootstrap allocator!");
//     // unsafe {
//     //    let allocator = kernel::memory::bootstrap_alloc::BootstrapAllocator::new(
//     //        bootstrap_alloc_region.base as usize, bootstrap_alloc_region.size as usize
//     //    );
//     //    BOOTSTRAP_ALLOCATOR.set(allocator).unwrap();
//     // }
//     // early_println!("Bootstrap allocator initialized.");
//     // Note: `Vec` and `String` in KernelConfig would now use this allocator.

//     // Initialize architecture-specific early CPU features
//     // kernel::arch::[target_arch]::cpu::early_setup::init();
//     // early_println!("Early CPU setup complete.");

//     // Setup basic panic handler (might have been done even earlier by kmain)
//     // kernel::panic::init();

//     // Transition to Stage 4: Core Kernel Subsystems Initialization
//     // kernel::core_init::initialize_core_subsystems(&KERNEL_CONFIG.get().unwrap(), BOOTSTRAP_ALLOCATOR.get().unwrap());
//     loop {}
// }
```

#### 3.4.7 Cross-Kernel Comparisons/Inspirations
*   **Linux:** `start_kernel()` function in `init/main.c` performs many early initializations: console setup, parsing command line, trap initialization, memory setup (memblock allocator before full buddy allocator), scheduler init, etc.
*   **seL4:** The initial thread (root task) receives `seL4_BootInfo` and is responsible for creating the system from the ground up using kernel objects. Its early setup is extremely minimal and focused on establishing the capability system. (seL4 Boot Process Ref).
*   **Redox OS:** `kmain` initializes logging, processes boot arguments, sets up PMM (Physical Memory Manager) and then VMM (Virtual Memory Manager). (Redox kmain Ref).
*   **Zircon:** The kernel starts, processes command line options from the bootloader, initializes a heap, and then starts creating essential kernel objects and threads. (Zircon Boot Process Ref).

#### 3.4.8 gRiTOS Requirements & Expectations
*   Establish a safe and stable Rust execution environment.
*   Securely process and validate all external information (bootloader data).
*   Provide basic debugging capabilities.
*   Prepare the ground for MMU/MPU initialization and other core kernel services.
*   Adherence to `GRITOS-REQ-FS-00008` (boot into a known safe state) is reinforced here by validating inputs and setting up basic fault handlers.

### 3.5 Stage 4: Core Kernel Subsystems Initialization

#### 3.5.1 Purpose
*   To initialize the fundamental subsystems of the gRiTOS kernel, transitioning it from a basic `no_std` environment to a more capable state.
*   To enable essential hardware services like memory protection (MMU/MPU), interrupt handling, and process scheduling.
*   To set up a proper memory management system, including a dynamic heap allocator if required (though with care for RT/FS).
*   To prepare the kernel to launch the first user-space process (Stage 5).

#### 3.5.2 Key Subsystem Initialization Activities

##### 3.5.2.1 Hardware Abstraction Layer (HAL) Initialization (`GRITOS-REQ-GEN-00012`)
Modules: `kernel::hal` (main), with sub-modules like `kernel::hal::timer`, `kernel::hal::interrupt_controller`, `kernel::hal::serial`, `kernel::hal::platform_specific`.
*   Initialize the HAL, which provides a consistent interface to platform-specific hardware. This might involve probing for hardware or using information from the DTB/ACPI tables.
*   Examples: platform timers, interrupt controllers, pin controllers.

##### 3.5.2.2 Memory Management Unit (MMU/MPU) Setup
*   **Physical Memory Manager (PMM):** Module: `kernel::memory::pmm`. Data Flow: Takes `KernelConfig.memory_map` to identify usable physical memory ranges. Provides functions like `alloc_frame()` and `free_frame()`.
*   **Virtual Memory Manager (VMM):** Module: `kernel::memory::vmm`. Data Flow: Uses `kernel::memory::pmm` to get frames for page tables. Creates and manages kernel address space mappings based on `KernelConfig.kernel_image_layout` and hardware requirements. Prepares to create user address spaces.
    *   Set up page tables (for MMU-based systems) or configure regions (for MPU-based systems) to establish virtual address spaces.
    *   Map kernel code and data sections with appropriate permissions (read-only, execute, no-execute). `GRITOS-REQ-GEN-00009`.
    *   Set up guard pages and stack canaries if not done earlier (`GRITOS-REQ-GEN-00010`).
    *   The kernel's own address space is finalized.
*   **Kernel Heap Initialization:** Module: `kernel::memory::heap_allocator` (e.g., `slab_allocator` or `buddy_allocator`). Data Flow: Initialized with a large region of virtual memory from the VMM (backed by PMM frames).

##### 3.5.2.3 Interrupt Handling System
Modules: `kernel::interrupts` (core dispatcher), `kernel::arch::[target_arch]::idt` or `::vector_table`. Data Flow: `kernel::hal::interrupt_controller` is configured. `kernel::interrupts` sets up handler routines that call into driver or subsystem specific handlers.
*   Initialize the interrupt controller (e.g., GIC for ARM, APIC for x86).
*   Register default interrupt handlers for hardware interrupts. These handlers will typically be responsible for acknowledging the interrupt, dispatching to specific driver handlers, and performing context switches if necessary.
*   Enable interrupts globally (or selectively) once handlers are in place. `GRITOS-REQ-RT-00005`.

##### 3.5.2.4 Scheduler and Process/Thread Management
Modules: `kernel::scheduler` (main), `kernel::thread` (TCB definition, context switching), `kernel::sync` (mutexes, semaphores). Data Flow: Scheduler uses `kernel::hal::timer` for preemption. `kernel::thread` module manages TCBs and their states.
*   Initialize scheduler data structures (e.g., run queues, priority lists). `GRITOS-REQ-RT-00002`, `GRITOS-REQ-RT-00003`.
*   Initialize structures for Task Control Blocks (TCBs) or Process Control Blocks (PCBs).
*   Create the initial "idle" thread/task for each CPU core. This task runs when no other task is ready.
*   Implement and initialize synchronization primitives (mutexes, semaphores, condition variables, spinlocks) (`GRITOS-REQ-GEN-00011`), considering priority inversion avoidance mechanisms (`GRITOS-REQ-RT-00004`).

##### 3.5.2.5 Timers and Clocks
*   Initialize system timers (e.g., PIT, HPET for x86; system timer for ARM) to provide timekeeping and enable preemptive scheduling (timer interrupts for timeslicing).

##### 3.5.2.6 Inter-Process Communication (IPC) Primitives (`GRITOS-REQ-CS-00007`, `GRITOS-REQ-CS-00023`)
Modules: `kernel::ipc` (main), with sub-modules like `kernel::ipc::endpoints`, `kernel::ipc::messages`, `kernel::ipc::capabilities` (if caps are tied to IPC). Data Flow: Initializes structures for managing IPC objects. Processes will later use syscalls routed to this module.
*   Initialize basic IPC mechanisms that the kernel will use to communicate with the first user-space processes and that those processes will use among themselves (e.g., channels, message queues, capability-based IPC endpoints).
*   This is fundamental for a microkernel architecture.

##### 3.5.2.7 Device Manager (Early)
Potentially initialize a rudimentary device manager that can discover essential boot devices or configure critical early hardware not handled by the HAL directly.

##### 3.5.2.8 Capability System Initialization (if applicable, `GRITOS-REQ-CS-00023`)
If gRiTOS uses a capability-based security model like seL4, this stage would involve setting up initial capability spaces and deriving capabilities for the first user-space process.

#### 3.5.3 Data Structures

##### 3.5.3.1 Page Map Entry (`PageMapEntry`, `PageFlags`)
```rust
// --- In kernel::memory::vmm ---
// Conceptual Page Map Entry (architecture dependent)
#[repr(transparent)]
pub struct PageMapEntry(u64); // Example for a 64-bit system

impl PageMapEntry {
    pub fn new(frame_addr: u64, flags: PageFlags) -> Self;
    pub fn set_addr(&mut self, frame_addr: u64);
    pub fn set_flags(&mut self, flags: PageFlags);
    pub fn is_present(&self) -> bool;
    // ... other methods
}
pub struct PageFlags; // Contains Present, Writable, Executable, UserAccessible etc.
```

##### 3.5.3.2 Physical Frame Descriptor (`PhysicalFrame`)
```rust
// --- In kernel::memory::pmm ---
// Conceptual Physical Frame Descriptor (optional, could be a bitmap too)
pub struct PhysicalFrame {
    // base_address: u64, // Implicit from index if an array/vec
    // ref_count: AtomicUsize, // Or other state
}
```

##### 3.5.3.3 Thread Control Block (`Tcb`, `ThreadState`)
```rust
// --- In kernel::thread ---
#[derive(PartialEq, Copy, Clone, Debug)]
pub enum ThreadState { Idle, Ready, Running, Blocked, Terminated }

// Conceptual Thread Control Block (TCB)
// #[repr(C)] // If context switch assembly needs specific layout
pub struct Tcb {
    pub id: ThreadId, // Unique thread ID
    pub process_id: ProcessId,
    pub state: ThreadState,
    pub priority: u8,
    pub timeslice: u32, // Remaining timeslice
    
    pub stack_pointer: usize, // Saved stack pointer for context switch
    pub kernel_stack_pointer: usize, // Stack used when running in kernel mode
    pub kernel_stack_base: usize,

    // pub instruction_pointer: usize, // Saved IP for context switch (part of context)
    pub context_data: ArchThreadContext, // Arch-specific registers, FPU state etc.

    pub address_space_id: AddressSpaceId, // Reference to its VAS
    // pub capabilities: CapabilitySet, // If using capabilities
    // pub ipc_blocked_on: Option<EndpointId>,
    // pub holding_locks: Vec<LockId>,
    // ... other scheduling info, resource tracking, IPC state
}
pub type ThreadId = u64;
pub type ProcessId = u64;
pub type AddressSpaceId = u64;
pub struct ArchThreadContext; // Placeholder for arch-specific context
```

##### 3.5.3.4 IPC Message Header (`IpcMessageHeader`)
```rust
// --- In kernel::ipc ---
// Conceptual IPC Message Header
pub struct IpcMessageHeader {
    pub sender_pid: ProcessId,
    pub receiver_endpoint_id: EndpointId, // Or target PID
    pub message_type: u32, // User-defined message type
    pub payload_length: usize,
    // pub capabilities_transferred: u8, // Count of caps
    // ... other flags or metadata
}
pub type EndpointId = u64;
```
Other data structures include PMM free lists/bitmaps, VMM page tables/VMA lists, kernel heap structures, scheduler run queues, interrupt controller configurations, IPC endpoint lists, and capability maps/CSpaces.

#### 3.5.4 Data and Control Flow
Stage 4 receives the `KernelConfig` (derived from `ArchBootInfo`) and the bootstrap allocator from Stage 3. It uses this configuration to initialize core hardware-dependent and hardware-independent kernel subsystems. The PMM is initialized first, using the memory map from `KernelConfig`. Then, the HAL is set up using device information from `KernelConfig` (DTB/ACPI) and the PMM for any required memory. The VMM uses the PMM to allocate frames for kernel page tables and maps the kernel itself. If a kernel heap is used, it's initialized using memory from VMM/PMM. Interrupts, scheduler, and IPC mechanisms are then initialized, relying on HAL components (timers, interrupt controllers) and memory services. Control flows from one subsystem initialization to the next, with dependencies managed (e.g., PMM before VMM, HAL before interrupt dispatch).

#### 3.5.5 Security Considerations
*   **Memory Protection (`GRITOS-REQ-GEN-00005`):** Correct MMU/MPU configuration is paramount. Errors can lead to lack of isolation between kernel and user space, or between user-space processes. Kernel memory must be protected from user-space access.
*   **Least Privilege for Kernel Code:** Apply Write XOR Execute (W^X) policies to kernel memory regions where possible. Data sections should not be executable; code sections should not be writable.
*   **Interrupt Security:** Ensure interrupt handlers are robust and do not leak information or introduce vulnerabilities. Prevent interrupt storms or unhandled interrupts from crashing the system.
*   **Scheduler Security:** The scheduler must prevent denial-of-service attacks (e.g., a low-priority process consuming excessive CPU time if not preempted correctly). Ensure fair scheduling and priority mechanisms work as intended.
*   **IPC Security (`GRITOS-REQ-CS-00007`):** IPC mechanisms must be secure, preventing unauthorized access to data or services and ensuring message integrity and authenticity if required. Capability-based IPC enhances this.
*   **Kernel Heap Integrity:** If a kernel heap is used, it must be protected from overflows, use-after-free, and other common allocator vulnerabilities (Rust helps, but logic errors are still possible).

#### 3.5.6 Implementation Notes & Code Examples
This stage involves significant `unsafe` code for MMU/MPU configuration, interrupt controller setup, and context switching. These should be heavily scrutinized and minimized. Rust's ownership and borrowing system can be very beneficial for managing resources like physical memory frames, capabilities, and IPC objects. Use of crates like `x86_64`, `cortex_m`, `riscv` for architecture-specific operations (paging, interrupts). Synchronization primitives like `Mutex` from `spin` or `parking_lot` (if alloc is available and appropriate for kernel use) would be used.

##### 3.5.6.1 Proposed Kernel Modules
```rust
// Conceptual kernel modules involved in Stage 4:
// pub mod kernel::hal::{self, timer, interrupt_controller, serial, platform_specific};
// pub mod kernel::memory::{self, pmm, vmm, heap_allocator, bootstrap_alloc}; // bootstrap_alloc used by pmm/vmm
// pub mod kernel::interrupts::{self, dispatcher};
// pub mod kernel::arch::[target_arch]::cpu::{self, idt, gdt, context, mmu};
// pub mod kernel::scheduler::{self, round_robin, priority_queue};
// pub mod kernel::thread::{self, tcb}; // TCB definition and management
// pub mod kernel::sync::{self, mutex, semaphore, spinlock};
// pub mod kernel::ipc::{self, endpoints, messages, channels, capabilities_ipc};
// pub mod kernel::device_manager; // Optional early device manager
```

##### 3.5.6.2 Conceptual `initialize_core_subsystems` Logic
```rust
// Conceptual function for Stage 4, called from Stage 3's early_init
// pub fn initialize_core_subsystems(config: &KernelConfig, bootstrap_alloc: &'static BootstrapAllocator) -> ! {
//     println!("Stage 4: Initializing Core Kernel Subsystems...");

//     // 1. Initialize Physical Memory Manager (PMM)
//     // Data Flow: KernelConfig.memory_map -> PMM's frame database
//     let pmm = kernel::memory::pmm::PhysicalMemoryManager::new(&config.memory_map, bootstrap_alloc);
//     // KERNEL_PMM.set(pmm).unwrap(); // Make it globally accessible
//     println!("PMM initialized. Total frames: {}, Usable frames: {}", pmm.total_frames(), pmm.usable_frames());

//     // 2. Initialize Hardware Abstraction Layer (HAL)
//     // Data Flow: KernelConfig (for DTB, ACPI etc.) -> HAL drivers
//     let hal = kernel::hal::Hal::initialize_platform_hal(&config, &pmm /*, bootstrap_alloc if needed */);
//     // KERNEL_HAL.set(hal).unwrap();
//     println!("HAL initialized for platform: {}", hal.platform_name());
//     // Early serial console might be re-initialized here using HAL serial driver.

//     // 3. Initialize Virtual Memory Manager (VMM) and Kernel Address Space
//     // Data Flow: PMM provides frames -> VMM creates kernel page tables
//     // Module: kernel::memory::vmm, kernel::arch::[target_arch]::cpu::mmu
//     let mut kernel_vas = kernel::memory::vmm::VirtualAddressSpace::new_kernel_space(&pmm, &config.kernel_image_layout);
//     kernel::arch::[target_arch]::cpu::mmu::load_kernel_page_tables(kernel_vas.get_root_page_table_addr());
//     kernel::arch::[target_arch]::cpu::mmu::enable_paging_and_protection();
//     // KERNEL_VAS.set(kernel_vas).unwrap();
//     println!("Kernel VMM and address space activated. Paging enabled.");

//     // 4. Initialize Kernel Heap (if used)
//     // Data Flow: VMM allocates virtual range -> PMM backs it -> Heap Allocator manages it
//     // kernel::memory::heap_allocator::init(&KERNEL_VAS.get().unwrap(), &KERNEL_PMM.get().unwrap());
//     // println!("Kernel heap initialized.");
//     // Now `alloc` crate types like Box, Vec (for kernel use) can function.

//     // 5. Initialize Interrupt Handling System
//     // Data Flow: HAL's interrupt_controller -> interrupts::dispatcher
//     kernel::interrupts::dispatcher::initialize(&hal.interrupt_controller);
//     kernel::arch::[target_arch]::cpu::idt::load_idt(); // Or similar vector table setup
//     kernel::interrupts::enable_hardware_interrupts(); // Unmask PIC or enable CPU IRQ flag
//     println!("Interrupt system initialized and enabled.");

//     // 6. Initialize Scheduler and current (idle) thread
//     // Data Flow: HAL's timer -> Scheduler for preemption ticks
//     // Modules: kernel::scheduler, kernel::thread
//     // let scheduler = kernel::scheduler::Scheduler::new(hal.get_scheduler_timer());
//     // KERNEL_SCHEDULER.set(scheduler).unwrap();
//     // kernel::thread::init_idle_thread_for_current_cpu(&KERNEL_SCHEDULER.get().unwrap());
//     // println!("Scheduler and idle thread initialized.");

//     // 7. Initialize IPC Subsystem
//     // kernel::ipc::init();
//     // println!("IPC subsystem initialized.");

//     // Transition to Stage 5: Prepare to launch the first user-space process
//     // kernel::process::manager::prepare_init_process_launch(&config);
//     // KERNEL_SCHEDULER.get().unwrap().start_scheduling(); // This call would not return

//     println!("Core subsystems initialized. Ready for first user process.");
//     loop {} // Or jump to scheduler start
// }
```

#### 3.5.7 Cross-Kernel Comparisons/Inspirations
*   **Linux:** `start_kernel()` continues with `setup_arch()`, `mm_init()`, `sched_init()`, `init_IRQ()`, `init_timers()`, etc. This is a very complex sequence.
*   **seL4:** The root task uses its initial capabilities and untyped memory to create kernel objects (like TCBs, CSpaces, VSpaces, Endpoints) and then starts other user-level components. The kernel itself is already running; this stage is more about the *first user-level task building the system*.
*   **Redox OS:** After PMM/VMM, it initializes ACPI, PCI, scheduler, filesystem context, and then starts loading drivers and finally the init process.
*   **Zircon:** Initializes various kernel objects (jobs, processes, threads), DPC (Deferred Procedure Call) queues, timer dispatcher, and then starts the first few essential user mode processes (driver_manager, component_manager).

#### 3.5.8 gRiTOS Requirements & Expectations for Core Subsystems
*   Robust and secure initialization of all core kernel functions.
*   Strict adherence to memory safety and protection (`GRITOS-REQ-GEN-00005`, `GRITOS-REQ-CS-00024`).
*   Enablement of deterministic scheduling and interrupt handling for real-time capabilities (`GRITOS-REQ-RT-*`).
*   The system is prepared to securely launch and isolate user-space code.
*   Compliance with `GRITOS-REQ-FS-00012` (justification of unsafe blocks) is critical here.

### 3.6 Stage 5: First User-Space Process Launch

#### 3.6.1 Purpose
*   To transition the system from kernel-only initialization to executing user-space code.
*   To launch the very first user-space process (often called `init`, `system_server`, `component_manager`, or a similar name), which will be responsible for orchestrating the rest of the system startup (Stage 6).
*   To ensure this first process is launched securely, with minimal necessary privileges and resources, adhering to the principle of least privilege (`GRITOS-REQ-CS-00004`).

#### 3.6.2 Key Activities

##### 3.6.2.1 Identifying the Init Process Image
Data Flow: Path or location of the init process image is retrieved from the `KernelConfig` structure (e.g., `KernelConfig.initrd_path` or a specific field for init process).
*   The kernel needs to locate the executable image for the first user-space process. This might be:
    *   Embedded in the kernel image itself (as a blob).
    *   Loaded from an initial RAM disk (initrd/initramfs) that was passed by the bootloader.
    *   Loaded from a specific location on a simple filesystem (if a very basic, trusted filesystem driver is part of the kernel).
*   The path or method to find this image might be hardcoded, specified via kernel command line, or discovered via a predefined protocol.

##### 3.6.2.2 Loading the Process Image
Module: `kernel::process::elf_loader`. Data Flow: `elf_loader` takes the image bytes, parses ELF headers, and identifies segments (code, data, BSS) and the entry point.
*   Read the executable file (e.g., ELF format) into memory.
*   Parse the ELF headers, map segments (code, data, BSS) into a newly created virtual address space for the process.
*   Set up the process's stack.

##### 3.6.2.3 Setting up Virtual Address Space (VAS)
Module: `kernel::process::vas_setup` (could be part of `kernel::memory::vmm` or `kernel::process`). Data Flow: `vas_setup` creates a new user VAS, maps ELF segments into it using VMM services, and allocates a user stack.

##### 3.6.2.4 Creating Process Control Block (PCB) / Task Control Block (TCB)
Module: `kernel::process::manager` or `kernel::thread::manager`. Data Flow: Populates the TCB with process ID, initial state, VAS reference, and entry point/stack pointer from `elf_loader` and `vas_setup`.

##### 3.6.2.5 Assigning Initial Capabilities/Resources (`GRITOS-REQ-CS-00023`, `GRITOS-REQ-CS-00025`)
Data Flow: The `process_manager` assigns a predefined minimal set of capabilities to the new TCB. These capabilities might be defined as constants or generated by a `kernel::security::capabilities` module.
*   In a capability-based system (like seL4, and an aim for gRiTOS), the kernel grants the initial process a minimal set of capabilities (e.g., to request more memory, to create other processes, to access specific hardware via kernel services, to use IPC endpoints).
*   If not strictly capability-based, traditional OSes might assign user/group IDs and resource limits.
*   The principle is to give it only what it absolutely needs to start the system.

##### 3.6.2.6 Setting the Entry Point and Initial State
*   Set the instruction pointer (e.g., `RIP` on x86-64, `ELR_EL1` on AArch64 if returning to EL0) in the process's context to its entry point (from ELF header).
*   Set the stack pointer.
*   Pass any necessary arguments or environment variables (though this is often minimal for the very first process).

##### 3.6.2.7 Adding to Scheduler Run Queue
Make the newly created process ready to run by adding it to the scheduler's run queue.

##### 3.6.2.8 Context Switch
The scheduler will eventually select this new process, and the kernel will perform a context switch to it. This involves saving the kernel's current context (if any specific thread was doing this work) and loading the new process's context, then transferring execution to the process's entry point in user mode.
*   This is the first transition to a privilege level lower than the kernel's (e.g., EL0 on ARM, Ring 3 on x86). `GRITOS-REQ-CS-00008`.

#### 3.6.3 Data Structures

##### 3.6.3.1 Thread Control Block (TCB) for Init Process
```rust
// --- In kernel::thread (reiteration/enhancement from Stage 4) ---
// Conceptual Thread Control Block (TCB)
// #[repr(C)]
pub struct Tcb {
    pub id: ThreadId,
    pub process_id: ProcessId, // Belongs to this process
    pub state: ThreadState,
    pub priority: u8,
    pub timeslice: u32,
    
    pub stack_pointer: usize, // Saved user-mode stack pointer
    pub instruction_pointer: usize, // Saved user-mode instruction pointer
    pub kernel_stack_pointer: usize,
    pub kernel_stack_base: usize,

    pub context_data: ArchThreadContext, // Arch-specific registers

    pub address_space_id: AddressSpaceId, // Refers to its VAS (e.g., a VmmObject or ID)
    pub capabilities: CapabilitySet,    // Capabilities granted to this thread/process
    pub parent_pid: Option<ProcessId>,
    // ... other fields
}
// (ThreadId, ProcessId, AddressSpaceId, ThreadState, ArchThreadContext previously defined)
```
Other structures include page tables and VAS structures for the init process, ELF loader internal structures, and the kernel stack for the init process's main thread.

##### 3.6.3.2 Capability Structures
```rust
// --- In kernel::security::capabilities (new) ---
#[derive(Debug, Clone, Copy, PartialEq, Eq)]
pub enum SystemCapability {
    CreateProcess,        // Allows creating new processes
    ManageMemory(AddressSpaceId), // Manage specific VAS
    AccessHwResource(u64), // Access a specific HW resource ID
    IpcCreateEndpoint,
    IpcConnectToEndpoint(EndpointId),
    // ... more granular capabilities
}

// Example of a simple capability set
pub struct CapabilitySet {
    // Using a bitmask for a small number of global caps for simplicity here.
    // A more complex system might use a list or a CSpace reference (like seL4).
    // For gRiTOS, this could be a Vec allocated from bootstrap_alloc or kernel heap.
    pub granted_caps: Vec<SystemCapability, &'static GlobalKernelAllocator>, // Example
}

impl CapabilitySet {
    pub fn new_minimal_for_init_process() -> Self {
        let mut caps = Vec::new_in(&*KERNEL_ALLOCATOR); // Example global allocator
        caps.push(SystemCapability::CreateProcess).unwrap();
        caps.push(SystemCapability::IpcCreateEndpoint).unwrap();
        // Add other essential caps for init to start basic services
        CapabilitySet { granted_caps: caps }
    }
    pub fn has_capability(&self, cap: SystemCapability) -> bool {
        self.granted_caps.contains(&cap)
    }
}
```

#### 3.6.4 Data and Control Flow
Stage 5 is initiated by the kernel after core subsystems are up. It uses `KernelConfig` to find the init process image (e.g., in an initrd). `kernel::process::elf_loader` reads this image. `kernel::process::vas_setup` creates a new virtual address space for the init process, mapping its segments. `kernel::process::manager` (or `kernel::thread::manager`) creates a TCB, populating it with information from the ELF load (entry point, stack) and the new VAS. `kernel::security::capabilities` provides a minimal set of capabilities for this TCB. The TCB is then added to the scheduler. When the scheduler picks this new thread, a context switch occurs, transferring control to the init process in user mode.

#### 3.6.5 Security Considerations
*   **Integrity of Init Process Image:** The executable for the init process must be verified cryptographically, similar to the kernel itself, if it's loaded from an untrusted source (e.g., initrd). This continues the chain of trust (`GRITOS-REQ-CS-00026`).
*   **Minimal Privileges (`GRITOS-REQ-CS-00004`, `GRITOS-REQ-CS-00025`):** This is paramount. The init process should not run with kernel privileges. It should have the absolute minimum set of permissions/capabilities required to perform its task of starting other system services.
*   **Isolation (`GRITOS-REQ-CS-00012`):** The init process must run in its own isolated virtual address space, protected from the kernel and other future processes.
*   **Resource Limits:** Apply resource limits (e.g., memory, CPU time if applicable) to prevent a buggy or compromised init process from consuming all system resources.
*   **Secure System Call Interface:** The init process will interact with the kernel via system calls. This interface must be secure and robust (`GRITOS-REQ-GEN-00007`, `GRITOS-REQ-GEN-00008`).
*   **No Secrets in Init Process Image:** Avoid embedding sensitive data directly in the init process image if it's stored on a generic initrd. Secrets should be provisioned via secure kernel mechanisms if needed.

#### 3.6.6 Implementation Notes & Code Examples
The kernel code for creating and launching the process involves complex interactions with the VMM, PMM, and scheduler. The init process itself will be a separate Rust crate, compiled as a user-space executable.

##### 3.6.6.1 Proposed Kernel Modules
```rust
// Conceptual kernel modules involved in Stage 5:
// pub mod kernel::process::elf_loader;    // Parses and loads ELF binaries.
// pub mod kernel::process::vas_setup;     // Sets up Virtual Address Spaces for new processes.
// pub mod kernel::process::manager;       // Manages process/thread creation, lifecycle.
// pub mod kernel::thread::tcb;            // TCB definition (re-iterated for clarity).
// pub mod kernel::security::capabilities; // Defines and manages capabilities.
// pub mod kernel::syscall::handler;       // Syscall interface used by init process (setup earlier).
```

##### 3.6.6.2 Conceptual `launch_first_user_process` Logic
```rust
// Kernel-side conceptual code (within kernel::process::manager or similar)
// fn launch_first_user_process(
//    config: &KernelConfig,
//    scheduler: &Scheduler, /* from KERNEL_SCHEDULER */
//    vmm: &Vmm, /* from KERNEL_VMM */
//    pmm: &Pmm  /* from KERNEL_PMM */
// ) -> Result<ProcessId, ProcessError> {
//     println!("Stage 5: Launching first user-space process...");

//     // 1. Get Init Process Image (e.g., from initrd specified in KernelConfig)
//     let init_image_data =뾰
//         config.initrd_base.map_or(Err(ProcessError::InitRdNotFound), |base| {
//             // Unsafe because we're trusting physical address from config
//             unsafe { core::slice::from_raw_parts(base as *const u8, config.initrd_size.unwrap_or(0) as usize) }
//         })?;
//     println!("Init process image located (size: {} bytes).", init_image_data.len());

//     // 2. Create a new address space for the process (using kernel::process::vas_setup)
//     let mut user_vas = kernel::process::vas_setup::create_user_vas(vmm, pmm)?;
    
//     // 3. Load ELF image into the VAS (using kernel::process::elf_loader)
//     let elf_info = kernel::process::elf_loader::load_elf_into_vas(
//         init_image_data, &mut user_vas, vmm, pmm
//     )?; // elf_info contains entry_point, stack_top suggestion etc.
//     println!("Init process ELF loaded. Entry point: {:#x}", elf_info.entry_point);

//     // 4. Assign initial capabilities
//     let initial_caps = kernel::security::capabilities::CapabilitySet::new_minimal_for_init_process();
//     println!("Initial capabilities assigned to init process.");

//     // 5. Create Process Control Block / TCB (using kernel::process::manager or kernel::thread)
//     let proc_id = ProcessId::new(1); // Typically PID 1 for init
//     let thread_id = ThreadId::new(1); 
//     let tcb = Tcb {
//         id: thread_id,
//         process_id: proc_id,
//         state: ThreadState::Ready,
//         priority: DEFAULT_INIT_PRIORITY, // Define this constant
//         timeslice: DEFAULT_TIMESLICE,    // Define this constant
//         stack_pointer: elf_info.initial_stack_pointer,
//         instruction_pointer: elf_info.entry_point,
//         kernel_stack_pointer: allocate_kernel_stack_for_thread(pmm)?.top(), // Example
//         kernel_stack_base: /* ... */,
//         context_data: ArchThreadContext::new_user_thread(), // Arch specific setup
//         address_space_id: user_vas.id(), // Assuming VAS has an ID
//         capabilities: initial_caps,
//         parent_pid: None,
//     };
//     println!("TCB for init process created (PID: {}, TID: {}).", proc_id, thread_id);
    
//     // Add TCB to process (if PCB/TCB are separate) and then to scheduler
//     // Process::add_thread(proc_id, tcb);
//     // scheduler.add_thread(tcb);
//     println!("Init process added to scheduler. Context switch will occur soon.");
    
//     Ok(proc_id)
// }
```

##### 3.6.6.3 Conceptual Init Process Stub
```rust
// main.rs for the init process (separate crate)
#![no_std]
// #![feature(start)] // May need a custom entry point if not using std's runtime

// extern crate gitos_user_lib; // A lib for syscalls, etc.

// #[start] // Or a custom _start symbol linked appropriately
// fn main(_argc: isize, _argv: *const *const u8) -> isize {
//     println_user!("gRiTOS Init Process: Started!");
    
//     // 1. Discover hardware / services via kernel syscalls
//     // 2. Launch device drivers (if they are user-space processes)
//     // 3. Launch other system services (networking, filesystem, etc.)
//     // 4. Eventually, launch a shell or application environment

//     println_user!("Init process entering main loop or exiting.");
//     0 // Exit code
// }
```

#### 3.6.7 Cross-Kernel Comparisons/Inspirations
*   **Linux:** The kernel starts `init` (usually `/sbin/init`, which can be systemd, SysVinit, etc.). `init` is PID 1. It's loaded from the root filesystem, which must be mounted first (often from an initramfs).
*   **seL4:** The root task (the first user-level component) is responsible for creating and managing all other user-level components and system services using the capabilities it was initially endowed with. This is a very powerful and flexible model. (seL4 Boot Process Ref).
*   **Zircon (Fuchsia):** Launches several key user mode processes early on, including `driver_manager`, `component_manager`, and `bootsvc`. `component_manager` is central to Fuchsia's component-based architecture. (Zircon Boot Process Ref).
*   **Redox OS:** The kernel starts `/sbin/init`, which is part of the `initfs` (initial RAM filesystem). This `init` process then mounts the actual root filesystem and proceeds to start services. (Redox Boot Process Ref, `init` program).

#### 3.6.8 gRiTOS Requirements & Expectations for First User Process
*   A clean, secure, and isolated launch of the first user-space process.
*   This process must be given the minimal capabilities necessary (`GRITOS-REQ-CS-00004`, `GRITOS-REQ-CS-00025`).
*   The mechanism for providing the init process image (e.g., initrd) must align with security and flexibility goals.
*   This stage is a critical step towards a functional microkernel system.

### 3.7 Stage 6: System Services and Application Initialization (Init Process Perspective)

#### 3.7.1 Purpose
*   To bring the gRiTOS system to its fully operational state by starting essential system services, device drivers (if running in user space), and eventually, applications.
*   This stage is orchestrated by the first user-space process launched in Stage 5 (referred to as the "Init Process" or "Service Manager").
*   To establish the user environment and make the system usable for its intended purpose.

#### 3.7.2 Key Activities (Init Process Perspective)

##### 3.7.2.1 Configuration Parsing
Module (Init Process): `init_process::config_parser`. Data Flow: Reads a file like `/etc/init.conf` or `/etc/services.d/*` -> `config_parser` deserializes this into a list of `ServiceDefinition` structs.

##### 3.7.2.2 Device Driver Initialization (User-Space Drivers)
Module (Init Process): `init_process::service_manager`. Data Flow: Iterates through `ServiceDefinition` list. For each service, uses `init_process::ipc_api` (which wraps kernel syscalls) to request process creation, passing executable path, arguments, and required capabilities (derived from `ServiceDefinition`).
*   If gRiTOS follows a strict microkernel design, many device drivers will be user-space processes. The Init Process is responsible for launching these driver processes.
*   Drivers might need to register themselves with a Device Manager service (also a user-space process). `GRITOS-REQ-GEN-00013`.

##### 3.7.2.3 Filesystem Mounting
*   Mounts the root filesystem (if not already done via an initramfs in kernel space).
*   Mounts other filesystems as per configuration (e.g., `/home`, `/tmp`, `/data`, special virtual filesystems like `/proc` or `/dev` if emulating POSIX).

##### 3.7.2.4 Networking Stack Initialization
Launches processes that manage the network interfaces, network configuration (DHCP/static IP), and network protocols. `GRITOS-REQ-CS-00018`.

##### 3.7.2.5 Starting Core System Services
Module (Init Process): `init_process::service_manager`. Data Flow: Iterates through `ServiceDefinition` list. For each service, uses `init_process::ipc_api` (which wraps kernel syscalls) to request process creation, passing executable path, arguments, and required capabilities (derived from `ServiceDefinition`).
*   Logging service.
*   Power manager.
*   Time synchronization service.
*   Cryptographic services.
*   Inter-Process Communication (IPC) router or name server (if not fully kernel-based).

##### 3.7.2.6 Security Policy Enforcement
May apply further security policies (e.g., Mandatory Access Control - MAC labels/rules) if a central policy engine is in user space. `GRITOS-REQ-CS-00001`.

##### 3.7.2.7 Resource Management
Manages system resources for services, potentially enforcing quotas or limits.

##### 3.7.2.8 Service Monitoring and Restart
The Init Process might monitor critical services and restart them if they fail (watchdog functionality for services).

##### 3.7.2.9 User Login/Shell (If applicable)
For general-purpose configurations, it might start a login manager or a shell on available terminals.

##### 3.7.2.10 Application Launch
Starts predefined applications or provides an environment for users/other processes to launch applications.

##### 3.7.2.11 Signaling Readiness
May signal to the kernel or other components that the system has reached a certain operational state (e.g., fully booted, networking up).

#### 3.7.3 Data Structures (Init Process Perspective)

##### 3.7.3.1 `ServiceDefinition` Structure
```rust
// --- In init_process ---
// Conceptual structure for defining a service to be launched by the Init Process.
// This would typically be parsed from a configuration file.
#[derive(Debug, Clone)]
pub struct ServiceDefinition {
    pub name: String,                           // e.g., "com.gritos.Driver.Serial"
    pub executable_path: String,                // Path to the service's executable
    pub arguments: Vec<String>,                 // Arguments to pass to the service
    pub user_id: UserId,                        // User ID to run the service as
    pub group_id: GroupId,                      // Group ID to run the service as
    // pub capabilities_needed: Vec<SystemCapability>, // Specific capabilities requested from kernel
    pub restart_policy: RestartPolicy,          // e.g., Never, OnFailure, Always
    pub dependencies: Vec<String>,              // Names of other services that must start first
    // pub resource_limits: ResourceLimits,
    // pub environment_vars: HashMap<String, String>,
}

pub type UserId = u32; // Or a more complex type
pub type GroupId = u32; // Or a more complex type

#[derive(Debug, Clone, Copy, PartialEq, Eq)]
pub enum RestartPolicy {
    Never,
    OnFailure,
    Always,
}
```
Other data structures include the service definition files/database, dependency graph for services, process table for managed services, and IPC handles for communicating with services and the kernel.

#### 3.7.4 Data and Control Flow (Init Process Perspective)
The Init Process, once started by the kernel (Stage 5), becomes the orchestrator. It begins by parsing its configuration files (e.g., from `/etc/init.conf` or `/etc/services.d/*`) using its `config_parser` module. These files define services, their executables, arguments, dependencies, and required privileges/capabilities. The `dependency_resolver` module processes this list to determine a valid start order. The `service_manager` then iterates through the ordered list. For each service, it uses an `ipc_api` (wrapper around kernel system calls) to request the kernel to create a new process. This request includes the executable path, arguments, user/group context, and a set of capabilities (which the kernel will vet and grant). The kernel's process management and ELF loading subsystems (from Stage 5) handle the actual creation and launch, returning a process ID to the Init Process. The Init Process then continues to launch other services and may monitor their health.

#### 3.7.5 Security Considerations
*   **Service Privileges (`GRITOS-REQ-CS-00025`):** Each system service and driver launched by the Init Process must run with the minimum privileges necessary for its operation. They should have their own user IDs, restricted capabilities, and isolated address spaces.
*   **Configuration Integrity:** The configuration files for the Init Process and the services it manages must be protected from unauthorized modification.
*   **Secure Inter-Service Communication:** IPC between services must be secure. If using a central IPC router, it must enforce appropriate policies.
*   **Attack Surface of Services:** Each running service contributes to the system's attack surface. Services should be designed securely and expose minimal interfaces. `GRITOS-REQ-CS-00017`.
*   **Fault Isolation:** A crash or compromise in one service should not cascade to other services or the Init Process itself (as much as possible). The microkernel architecture helps significantly here.
*   **Auditing and Logging (`GRITOS-REQ-CS-00003`):** The Init Process and other services should generate logs for security-relevant events.

#### 3.7.6 Implementation Notes & Code Examples
The Init Process and most system services would be implemented in Rust, benefiting from its safety features. They would use a gRiTOS user-space library for system calls, IPC, and other OS interactions.

##### 3.7.6.1 Proposed Init Process Modules
```rust
// Conceptual modules for the Init Process (user-space):
// pub mod init_process::main;                // Entry point and main loop.
// pub mod init_process::config_parser;       // Reads and parses service definitions.
// pub mod init_process::service_manager;     // Manages service lifecycle (start, stop, monitor).
// pub mod init_process::dependency_resolver; // Orders services based on dependencies.
// pub mod init_process::ipc_api;             // Wrappers for syscalls to request kernel services.
// pub mod init_process::logger;              // For init process's own logging.
```

##### 3.7.6.2 Conceptual `init_main` and Service Launch Logic
```rust
// Conceptual main function for an Init Process in Rust (user-space)
// fn init_main() { // Resides in init_process::main
//     let logger = init_process::logger::init(); // Initialize its own logging
//     logger.info("gRiTOS Init Process: Stage 6 - System Services Initialization.");

//     // 1. Parse service configuration
//     // Data Flow: Filesystem -> raw_config_bytes -> Vec<ServiceDefinition>
//     let service_definitions = match init_process::config_parser::load_all_configs("/etc/services.d") {
//         Ok(defs) => defs,
//         Err(e) => {
//             logger.error("Failed to load service definitions: {:?}. Halting.", e);
//             // Communicate critical failure to kernel? Or attempt minimal shell?
//             loop { /* halt or panic */ }
//         }
//     };
//     logger.info("Loaded {} service definitions.", service_definitions.len());

//     // 2. Resolve dependencies and determine start order
//     let ordered_services_names = match init_process::dependency_resolver::resolve(&service_definitions) {
//         Ok(order) => order,
//         Err(e) => {
//             logger.error("Failed to resolve service dependencies: {:?}. Halting.", e);
//             loop { /* halt or panic */ }
//         }
//     };
//     logger.info("Service dependencies resolved.");

//     // 3. Launch services (using init_process::service_manager and init_process::ipc_api)
//     // Data Flow: ServiceDefinition -> ipc_api calls to kernel -> kernel creates process
//     let mut service_manager = init_process::service_manager::ServiceManager::new(logger.clone());
//     for name in ordered_services_names {
//         if let Some(service_def) = service_definitions.iter().find(|s| s.name == name) {
//             logger.info("Requesting start of service: {}", service_def.name);
//             match service_manager.launch_service(service_def) { // launch_service uses ipc_api internally
//                 Ok(pid) => logger.info("Service '{}' started with PID {}.", service_def.name, pid),
//                 Err(e) => logger.error("Failed to start service '{}': {:?}", service_def.name, e),
//             }
//         }
//     }
    
//     logger.info("All primary services launched. System operational.");

//     // Enter a monitoring loop
//     loop {
//         service_manager.monitor_services();
//         // platform_specific_wait_for_event_or_timeout();
//     }
// }

// Conceptual part of init_process::service_manager
// fn launch_service(&mut self, service_def: &ServiceDefinition) -> Result<ProcessId, ServiceError> {
//     // Use init_process::ipc_api to make a syscall or send an IPC message to the kernel
//     // requesting process creation.
//     let request = CreateProcessRequest {
//         path: &service_def.executable_path,
//         args: &service_def.arguments,
//         user: service_def.user_id,
//         group: service_def.group_id,
//         // capabilities: determine_kernel_caps_from(service_def.capabilities_needed),
//     };
//     
//     match init_process::ipc_api::request_create_process(&request) {
//         Ok(pid) => {
//             // Store PID, manage service state
//             self.running_services.insert(pid, service_def.clone());
//             Ok(pid)
//         }
//         Err(ipc_err) => Err(ServiceError::LaunchFailed(ipc_err)),
//     }
// }
```

#### 3.7.7 Cross-Kernel Comparisons/Inspirations
*   **Linux (systemd/SysVinit):** `systemd` is a complex service manager that handles starting services in parallel based on dependencies defined in unit files. SysVinit uses sequential shell scripts. Both are responsible for bringing the system to a multi-user state.
*   **macOS (launchd):** `launchd` is responsible for starting daemons, agents, and scripts based on property list files. It supports on-demand launching and dependency management.
*   **Windows (Service Control Manager - SCM):** The SCM starts and stops services based on registry settings and dependencies.
*   **Zircon (Fuchsia - component_manager):** `component_manager` is responsible for resolving and running components (which can be services, drivers, applications) based on a component manifest. This is a core part of Fuchsia's architecture. (Fuchsia Components Ref).
*   **Redox OS (init):** The Redox `init` program is simpler than systemd but serves a similar role in starting basic services and eventually a shell.

#### 3.7.8 gRiTOS Requirements & Expectations for System Initialization
*   A robust and secure mechanism for managing the lifecycle of system services and user-space drivers.
*   Strict adherence to the principle of least privilege for all launched components (`GRITOS-REQ-CS-00025`).
*   The system should reach a well-defined operational state, fulfilling its intended purpose (e.g., as an embedded controller, a general-purpose system).
*   The design of the Init Process / Service Manager should align with the overall gRiTOS goals of security, safety, and reliability.
*   Consideration for `GRITOS-REQ-GEN-00014` (transactional updates) and `GRITOS-REQ-GEN-00015` (independent component updates) might influence how services are managed and updated.

This concludes the detailed elaboration of the high-level boot stages. The next section could focus on specific cross-cutting concerns like fault handling during boot or detailed memory layout.

## 4. Requirements Traceability

The gRiTOS boot sequence, as detailed in the preceding sections, is designed to inherently address many of the specific requirements outlined in `gritOS Requirements.md`. This section highlights how key requirements related to booting, security, and safety are met.

### 4.1 Mapping to Functional Safety (FS) Requirements
*   **GRITOS-REQ-FS-00008: The OS shall boot into a known safe state.**
    *   **Met by:** The entire boot sequence is designed around this.
        *   **Stage 0-1 (Firmware/Bootloader):** Secure boot mechanisms aim to ensure only trusted code is loaded.
        *   **Stage 2 (Kernel Verification):** Cryptographic verification of the kernel ensures its integrity before execution. If verification fails, the system should halt or enter a defined error state, not an unsafe one.
        *   **Stage 3 (Early Kernel Init):** Validation of bootloader-provided information (memory map, command line) prevents the kernel from starting with corrupt data. Early exception handlers are set up.
        *   **Stage 4 (Core Subsystems):** Orderly initialization of MMU/MPU, interrupt handlers, and other critical systems. Errors here should lead to a controlled halt or defined fault state.
        *   **Stage 5 (First User Process):** The first user process is launched with minimal privileges. Its failure should not compromise kernel integrity.

*   **GRITOS-REQ-FS-00009: The OS SHOULD follow best practices described and implemented in the seL4 microkernel.**
    *   **Met by:** Several aspects of the boot philosophy and detailed stages draw inspiration from seL4:
        *   Emphasis on minimality in trusted components (Boot Philosophy).
        *   Strong focus on verification at each stage (Stage 2 for kernel, and implicitly for bootloader).
        *   Concept of explicit resource management and capability-based security (mentioned as goals for Stage 4 and 5).
        *   The idea of a root task/first user process being endowed with minimal capabilities to build the rest of the system (Stage 5).

*   **GRITOS-REQ-FS-00012: The OS development process shall include a rigorous methodology for identifying, managing, and formally justifying all unsafe code blocks within the Rust codebase.**
    *   **Met by:** While this is a process requirement, the boot sequence documentation highlights where `unsafe` Rust code is unavoidable (e.g., Stage 3 for MMIO, Stage 4 for MMU/interrupt setup). The Rust-Specific Notes in each stage serve as a starting point for identifying these areas. The boot philosophy also emphasizes minimizing and encapsulating `unsafe` code.

### 4.2 Mapping to Cybersecurity (CS) Requirements
*   **GRITOS-REQ-CS-00004: The OS design shall adhere to the Principle of Least Privilege.**
    *   **Met by:**
        *   **Stage 5 (First User Process):** Explicitly states that the init process is launched with minimal necessary privileges/capabilities.
        *   **Stage 6 (System Services):** Extends this principle to all system services and user-space drivers launched by the init process.
        *   The microkernel approach itself, where drivers and services run in user-space, is a direct application of this principle.

*   **GRITOS-REQ-CS-00005: The OS shall implement a secure boot chain and verify its integrity.**
    *   **Met by:** This is a cornerstone of the boot sequence.
        *   **Stage 0 (Firmware):** Relies on platform Secure Boot (e.g., UEFI Secure Boot) to verify the Stage 1 bootloader.
        *   **Stage 1 (Bootloader):** If trusted, this stage is responsible for cryptographically verifying the gRiTOS kernel (Stage 2).
        *   **Stage 2 (Kernel Verification):** If the Stage 1 bootloader is not fully trusted or capable, a small trusted loader within gRiTOS verifies the main kernel. This ensures a continuous chain of trust.

*   **GRITOS-REQ-CS-00006: The OS shall integrate with a Hardware Root of Trust (HRoT).**
    *   **Met by:**
        *   **Stage 0 (Firmware):** Secure Boot mechanisms are typically anchored in an HRoT (e.g., immutable Boot ROM, TPM-based measurements). The boot sequence assumes and leverages such capabilities if present on the hardware.

*   **GRITOS-REQ-CS-00008: The OS shall ensure secure transitions between user and kernel modes.**
    *   **Met by:**
        *   **Stage 4 & 5:** The setup of MMU/MPU and distinct address spaces, along with a well-defined system call interface (planned for Stage 4 development), are key to secure mode transitions. The first transition occurs when the kernel context switches to the init process in Stage 5.

*   **GRITOS-REQ-CS-00009: The OS shall cryptographically verify the integrity and authenticity of the bootloader and kernel.**
    *   **Met by:**
        *   **Stage 0/1:** Firmware/Bootloader verifies the next stage bootloader or kernel.
        *   **Stage 2:** Explicitly details kernel image verification (signature checking) before execution.

*   **GRITOS-REQ-CS-00012: The OS shall enforce strict isolation between processes and the kernel.**
    *   **Met by:**
        *   **Stage 4 (Core Subsystems):** Initialization of MMU/MPU is designed to create separate, protected address spaces for the kernel and for each user-space process.
        *   **Stage 5 (First User Process):** The init process is launched into its own isolated VAS.

*   **GRITOS-REQ-CS-00016: The OS shall minimize its trusted computing base (TCB).**
    *   **Met by:**
        *   The microkernel design inherently aims to minimize the kernel's TCB.
        *   The boot sequence itself, by being modular and aiming for verifiable components (especially the kernel loader/verifier in Stage 2), supports this. The philosophy emphasizes minimality in critical boot components.

*   **GRITOS-REQ-CS-00023: The OS shall implement capability-based security.**
    *   **Met by:** While full implementation is part of overall OS development, the boot sequence prepares for this.
        *   **Stage 4 (Core Subsystems):** Notes the potential initialization of a capability system.
        *   **Stage 5 (First User Process):** Describes assigning initial, minimal capabilities to the first user process, drawing inspiration from systems like seL4.

*   **GRITOS-REQ-CS-00024: The OS shall ensure memory safety, eliminating buffer overflows, use-after-free, and double-free vulnerabilities.**
    *   **Met by:** This is a primary reason for choosing Rust.
        *   **Boot Philosophy & Rust-Specific Notes (All Stages):** The use of Rust for kernel and bootloader components (where feasible) is central. The boot sequence documentation calls out `unsafe` blocks where necessary, which are the primary areas to scrutinize for potential memory safety issues that Rust's compiler cannot statically guarantee.
        *   **Stage 4 (Core Subsystems):** MMU/MPU setup provides hardware-enforced memory protection against many common exploits, complementing Rust's compile-time safety.

*   **GRITOS-REQ-CS-00025: Every component (drivers, applications) shall run in an isolated environment with minimal privileges.**
    *   **Met by:** This is a direct consequence of the microkernel design and principle of least privilege.
        *   **Stage 5 (First User Process):** The init process is the first example.
        *   **Stage 6 (System Services):** This stage explicitly describes launching drivers and services as separate processes with minimal privileges.

*   **GRITOS-REQ-CS-00026: The OS shall ensure the integrity of the boot process and the entire software stack.**
    *   **Met by:** The chain of trust established from Stage 0 (Hardware Secure Boot) through Stage 1 (Bootloader verifying Kernel) and Stage 2 (Kernel verification) directly addresses this. Integrity of the init process image is also mentioned in Stage 5.

### 4.3 Mapping to General OS Requirements
*   **GRITOS-REQ-GEN-00001: All components, including build and configuration tools, shall be written in Rust.**
    *   **Met by:** The boot sequence advocates for Rust implementation for gRiTOS-specific bootloader components (Stage 1), the kernel loader (Stage 2), and the entire kernel (Stages 3 onwards). It acknowledges that Stage 0 (firmware) and parts of Stage 1 (standard bootloaders like GRUB/U-Boot) are outside this scope.

*   **GRITOS-REQ-GEN-00005: The OS shall provide memory protection between processes/tasks.**
    *   **Met by:** **Stage 4 (Core Subsystems)** where MMU/MPU is initialized and configured to create separate address spaces.

*   **GRITOS-REQ-GEN-00006: The OS shall use deterministic memory allocation, avoiding dynamic allocation in critical paths.**
    *   **Met by:**
        *   **Stage 3 (Early Kernel Init):** Suggests using a bump allocator or static slab allocator for early kernel needs.
        *   **Stage 4 (Core Subsystems):** Mentions initializing a kernel heap but with the caveat "with careful consideration for RT/FS." This implies that critical paths should use pre-allocated buffers or specialized allocators rather than general-purpose dynamic allocation.

*   **GRITOS-REQ-GEN-00008: The OS shall perform thorough validation of system call parameters.**
    *   **Met by:** While system call implementation is detailed in Stage 4/5 of the *Development Plan*, the boot sequence (Stage 5 Security Considerations) notes that the init process interacts via a secure system call interface, implying parameter validation is crucial.

*   **GRITOS-REQ-GEN-00009: The OS shall establish separate address spaces for kernel and user processes.**
    *   **Met by:** **Stage 4 (Core Subsystems)** during VMM initialization and MMU/MPU setup. The first user process gets its own VAS in **Stage 5**.

This mapping demonstrates that the designed boot sequence provides a solid foundation for meeting many of the critical requirements of gRiTOS, particularly in establishing a secure and safe startup environment. Further details for some requirements will be elaborated during the development of the respective kernel modules.

## 5. Conclusion
This document has laid out a comprehensive, staged boot sequence for gRiTOS, designed with foundational principles of security, safety, and reliability. Each stage, from initial hardware power-on through to the full initialization of system services, incorporates considerations for these principles. The detailed breakdown of activities, data structures, module interactions, and requirements traceability aims to provide clear, actionable guidance for the development and implementation of a robust gRiTOS startup process. Adherence to this specification will be crucial in achieving the overall quality and trustworthiness goals of the gRiTOS project.
