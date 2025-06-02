# Initialization (`init`) Development Plan

## Overview

This document details the development plan for the Initialization (init) subsystem of GritOS. This module is located at `src/init`. It is responsible for orchestrating the kernel's boot sequence after the very early architecture-specific setup. It's analogous to Linux's `init/main.c`.

## Responsibilities

*   Coordinate the initialization of various kernel subsystems in the correct order.
*   Parse kernel command-line arguments (if any).
*   Set up early services and drivers.
*   Transition from boot-time environment to full operational state.
*   Eventually, prepare and start the first user-space process.

## Key C Components (Linux Inspiration - for translation to Rust)

*   `init/main.c`: `start_kernel()` function and its callees.
*   `init/do_mounts.c`: Mounting initial filesystems.
*   `init/initramfs.c`: Loading initramfs.
*   Kernel command-line parsing logic.
*   `do_initcalls` or similar mechanisms for ordered initialization.

## Rust Implementation Strategy

*   **Main Initialization Function:** A Rust function within `src/init/mod.rs` (e.g., `kernel_init()`) that is called from `src/lib.rs` after very early setup.
*   **Initialization Stages:** Define clear stages for initialization (e.g., pre-scheduler, devices, filesystems).
*   **Subsystem Initialization Calls:** Call public initialization functions from `mm`, `arch`, `drivers`, `fs` modules.
*   **Command-Line Parsing:** Implement or use a crate for parsing command-line arguments.

## Architecture-Specific Considerations

The `init` module is responsible for the *orchestration* of kernel initialization, but many of the specific initialization routines it calls are architecture-dependent and are implemented within the `src/arch/[architecture]/` modules.

*   **Boot Process:** The very early boot process, including how the kernel is loaded and entered, is handled by architecture-specific code in `src/arch/[architecture]/boot.s` (or similar) and early Rust functions in `src/arch/`. The `init` module typically takes over after this initial CPU and basic memory setup.
*   **Hardware Initialization:** Initialization of core hardware like interrupt controllers (PIC/APIC/GIC), timers (PIT/HPET/ARM Timers), and MMU setup is performed by calling functions within the currently targeted `arch` module. The `init` module will call generic functions like `arch::initialize_interrupt_controller()` or `arch::initialize_timer()`, which then dispatch to the specific implementation based on conditional compilation in `src/arch/mod.rs`.
*   **CPU Features:** Enabling specific CPU features (e.g., FPU/SIMD units, security features) is done via the `arch` module.
*   **Memory Map and Layout:** While the `mm` module manages memory, the initial discovery of the memory map and understanding of architecture-specific memory layouts (e.g., device memory regions, RAM layout) are provided by or through the `arch` module to `mm` and `init`.
*   **Console Initialization:** The specific method for initializing the early console (e.g., serial port setup, VGA text mode) is often architecture-dependent and handled by drivers that may themselves have `arch`-specific components or be called by `arch`-specific init code before the main `init` sequence takes full control.

The `init` module's goal is to be as architecture-agnostic as possible in its main sequencing logic, relying on the `arch` module to provide the necessary abstractions and implementations for hardware-level tasks. The order of initialization calls made by `init` might also be influenced by architectural requirements (e.g., MMU must be up before certain drivers can be probed).

## Development Phases

The main plan is located at [../../grit_dev_plan.md](../../grit_dev_plan.md).

**Current Plan (Phase 1 of GritOS Development):**

1.  **Phase 1: Early Kernel Setup (Corresponds to `start_kernel` initial steps):**
    *   Initialize console (VGA, serial - via `drivers` module).
    *   Basic memory information (e.g., from bootloader, passed to `mm` module for early allocator).
    *   Parse kernel command line.
    *   **1.1. Bootstrapping & Early Arch-Specific Setup (Leveraging `rust_kernel` foundation):**
        *   Bootloader Integration: Solidify use of the `bootloader` crate (or confirm current boot image approach via `bootimage` and `run-command` in `Cargo.toml`).
        *   Minimal GDT, TSS, & IDT Setup: Ensure robust Global Descriptor Table (GDT), Task State Segment (TSS), and Interrupt Descriptor Table (IDT) setup in Rust (delegated to `arch` module).
            *   `GDT Contents: Must include Kernel Code Segment, Kernel Data Segment, and a TSS Segment Descriptor.`
            *   `TSS Definition: Define a TSS structure, primarily for specifying IST pointers for critical fault handlers (e.g., Double Fault, NMI) as planned in 1.2.3.`
            *   `Load Task Register (LTR): After GDT and TSS are set up, load TR with the selector for the TSS descriptor.`
            *   `IDT Base Setup: Initialize the IDT structure itself; specific handlers will be populated in 1.2.3.`
        *   Basic Paging Setup: Establish initial page tables (e.g., PML4, PDPT, Page Directory) for kernel space.
            *   `Identity Map Kernel: Identity map the kernel's code and data sections with appropriate permissions (RX for code, RW for data/BSS/stack). This requires knowledge of kernel's physical memory layout from the linker.`
            *   `Page Table Flags for Security: Ensure initial page table entries correctly set Present and Writeable flags. Crucially, configure pages to support the NX (No-Execute) feature (e.g., data pages non-executable), which will be fully enabled in 1.2.5.`
            *   `Early MMIO Mapping: Map essential MMIO regions required before full driver initialization.`
                *   `VGA Buffer: Explicitly mapped to a virtual address (e.g., `0xFFFF_8000_000B_8000`) using the bootloader-provided page table and a frame allocator in `mm::paging::map_vga_buffer`. The VGA driver (`drivers/vga_buffer.rs`) now uses this virtual address.`
                *   `Serial Port MMIO: Currently accessed via I/O ports; direct MMIO mapping for serial not yet implemented if applicable.`
        *   Early Serial Console: Configure and enable the serial port very early for reliable diagnostic output. This serial interface will be critical for the panic handler and the logging system planned in 1.2.2. Consider basic locking if re-entrancy during panic is a concern.
        *   Robust Panic Handler: Ensure the panic handler can output information via serial and VGA reliably.
            *   `Contextual Information: Dumps all general-purpose registers (RAX, RBX, RCX, RDX, RSI, RDI, RBP, RSP, R8-R15) using inline assembly in the main panic handler (`src/main.rs`). Also prints panic message, file/line location. Output is attempted via `kprintln!` first, then via raw VGA/serial writes.`
            *   `Timestamping (Best Effort): If a rudimentary time source (e.g., very early RTC access or basic cycle counter) is feasible before full timekeeping in 1.2.6, consider adding a timestamp to panic messages.`
    *   **1.2. Transition to Rust `kernel_init` (Equivalent to Linux `start_kernel` functionality):**
        *   **1.2.1. Kernel Command Line Parsing:**
            *   **Status:** Implemented.
            *   **Location:** `src/init/cmdline.rs`.
            *   **Functionality:**
                *   Parses the kernel command line string provided by the bootloader (`boot_info.kernel_command_line`).
                *   Supports basic space-separated `key=value` pairs (e.g., `init=/sbin/init`, `root=/dev/sda1`). Does not currently support quoted values or complex shell-like parsing.
                *   Recognized parameters include `init` (for init path) and `root` (for root device). Unknown parameters are logged.
            *   **Storage:** Parsed arguments are stored in a `KernelCommandLineArgs` struct, which is held in a `static spin::Once<KernelCommandLineArgs>` named `PARSED_ARGS` within `src/init/cmdline.rs`.
            *   **Access:** Global access to parsed arguments is provided by `crate::init::cmdline::get_cmdline_args()`.
            *   **Initialization:** The parsing is initiated by calling `crate::init::cmdline::init_cmdline()` from `kmain` in `src/main.rs` after basic console setup.
            *   Logging of raw and parsed arguments is performed using `kprintln!`.
        *   **1.2.2. Early Logging Infrastructure (`printk` equivalent):** Develop a buffered kernel logging system (macros `kprint!`, `kprintln!`), outputting to serial console (and VGA if feasible). Consider log levels and timestamps. (Current `serial_println` and `vga_print` are good starts).
        *   **1.2.3. Trap & Interrupt Initialization:**
            *   Complete IDT setup with Rust handlers for all standard CPU exceptions (Divide Error, Debug, NMI, Breakpoint, Overflow, Page Fault, General Protection Fault, etc.) (delegated to `arch` and `interrupts` modules). Ensure handlers log comprehensive context (register dumps, faulting addresses, error codes) for debugging.
            *   Implement robust fault handling: For critical exceptions like Double Fault and NMI, ensure handlers use a separate, known-good stack via an Interrupt Stack Table (IST) entry in the Task State Segment (TSS) to prevent triple faults.
                *   `Define IST pointers (typically 1 to 7 available) within the TSS structure for dedicated stacks.`
                *   `The TSS segment itself needs a descriptor in the GDT.`
                *   `Load the Task Register (ltr) with the GDT selector for the TSS.`
                *   `Configured IDT entries for Double Fault (vector 8, IST index 1) and NMI (vector 2, IST index 2) to use their dedicated stacks defined in the TSS. This ensures robust handling of these critical exceptions.`
            *   Initialize hardware interrupt controllers: Prioritize I/O APIC setup if available (requires ACPI/MP table parsing for detection and configuration). Fall back to PIC (8259) for basic interrupt handling.
                *   `I/O APIC Discovery: Parse ACPI MADT (Multiple APIC Description Table) or legacy Intel MP (MultiProcessor) Tables to find I/O APIC base addresses, IDs, and IRQ source override information.`
                *   `I/O APIC Redirection: For each hardware IRQ, program a redirection table entry in the relevant I/O APIC. This includes setting the target interrupt vector, destination Local APIC(s), delivery mode, trigger mode (edge/level), and polarity.`
                *   `Masking/Unmasking: Implement functions to mask and unmask individual IRQs at the I/O APIC redirection table entry level.`
            *   Handle spurious IRQs: Implement graceful handling for spurious interrupts from both PIC (typically IRQ7) and I/O APIC.
                *   `PIC Spurious IRQs: Implemented logic in `rust_irq_handler_callback` (called from IDT stubs) to check for spurious IRQs on lines 7 (master) and 15 (slave). This involves:
                        *   A new method `Pics::is_spurious_irq(irq_line)` in `pic8259.rs` reads the In-Service Register (ISR) of the relevant PIC.
                        *   If the ISR bit for IRQ7 (on the respective PIC) is not set, the interrupt is logged as spurious. EOI is still sent to ensure PIC state remains consistent.`
                *   `I/O APIC Spurious IRQs: Not yet applicable as I/O APIC is not implemented.`
            *   For a more detailed plan on enhancing interrupt handling, see the [Interrupt Development Plan](../interrupts/interrupt_dev_plan.md).
        *   **1.2.4. Initial Physical Memory Management (Pre-Allocator):**
            *   Retrieve `memory_regions` from `BootInfo` in `kmain`. (Already done for printing).
            *   Sanitize and consolidate memory map: Process the bootloader-provided memory map to merge adjacent free regions and correctly account for reserved or unusable small areas.
                *   `Consolidation Algorithm: Typically, sort all memory regions by their start address. Then, iterate through the sorted list, merging a region with the previous one if they are contiguous (`prev_region.end == current_region.start`) and share the same type (e.g., both are 'usable').`
                *   `Minimum Usable Region Size: Define a threshold (e.g., one page) for usable memory regions. Discard or mark as unusable any free regions smaller than this to simplify allocator logic.`
            *   Define data structures for a basic frame allocator (e.g., `BootInfoFrameAllocator` to store usable regions) in `src/mm/frame_allocator.rs`. (Note: `BootInfoFrameAllocator` from `bootloader` crate is used initially, then a custom `BitmapFrameAllocator` takes over).
            *   Implement `mm::init_frame_allocator(&boot_info.memory_regions)`:
                *   Initializes a `BitmapFrameAllocator` using the largest usable memory region found in `BootInfo`.
                *   **Reservation of Critical Areas:**
                    *   The `BitmapFrameAllocator::init` function now accepts an optional list of `PhysFrameRange`s to be pre-reserved.
                    *   `init_frame_allocator` defines placeholder physical ranges for the kernel image (e.g., `0x100000` - `0x800000`) and the VGA buffer (`0xb8000`). These ranges are passed to `BitmapFrameAllocator::init`.
                    *   Frames within these reserved ranges that fall into the allocator's managed region are marked as used in the bitmap during initialization.
                    *   **Note:** Kernel image addresses are currently placeholders and need to be replaced with actual values from linker symbols for full accuracy.
                *   **Limitations:**
                    *   Currently uses only the single largest usable region, not all usable regions.
                    *   Does not yet perform advanced memory map sanitization (merging regions) beyond selecting the largest one.
                    *   Reservation of bootloader-specific structures is not yet explicitly handled unless they fall within the placeholder kernel region.
            *   Define `FrameAllocator` trait early: Introduce a generic `FrameAllocator` trait (e.g., with `allocate_frame`, `deallocate_frame` methods) that `BitmapFrameAllocator` implements. This facilitates easier integration of more advanced allocators later.
                *   `Core Trait Methods: `fn allocate_frame(&mut self) -> Option<PhysFrame>;` and `fn deallocate_frame(&mut self, frame: PhysFrame);`. (Note: `PhysFrame` type from `x86_64` crate or similar).`
                *   `Potential Extensions: Consider methods like `fn allocate_frames(&mut self, count: usize) -> Result<impl ExactSizeIterator<Item = PhysFrame>, AllocationError>;` or `fn allocate_contiguous(&mut self, frame_count: usize) -> Option<PhysFrameRange>;` for more complex needs.`
            *   Log pre-allocator state: After initialization, log the total amount of usable memory managed by the `BitmapFrameAllocator`.
            *   Call `mm::init_frame_allocator` from `kmain` after printing memory map.
            *   (Note: Actual frame allocation/deallocation logic is part of task 1.3.1).
        *   **1.2.5. CPU Feature Detection & Setup:**
            *   Rust implementation for `CPUID` instruction to detect CPU features (in `arch` module).
            *   Initialize per-CPU structures (stub for now): Lay basic groundwork for per-CPU data (e.g., CPU ID, local timer data). While initially a simple static allocation for UP systems, this stub should be designed for future SMP enhancement (e.g., dynamic allocation and access via segment registers like `GS` or `FS`).
                *   `Mechanism for x86_64: On entering kernel mode (e.g., via syscall or interrupt), the `SWAPGS` instruction can be used to swap the user `GS` base with a kernel-defined `GS` base (stored in `IA32_KERNEL_GS_BASE` MSR).`
                *   `The kernel then sets the `IA32_KERNEL_GS_BASE` MSR for each CPU to point to its unique per-CPU data area.`
                *   `Kernel code can then access per-CPU variables using memory operands with the `gs` segment override prefix (e.g., `mov rax, gs:[offset_of_variable]`).`
            *   Enable essential CPU security features: Detect support for and enable NXE, SMEP, and SMAP via the `arch::x86_64::cpuid` module.
                *   `NXE (No-Execute Enable): Detected via CPUID. Enabled by setting the `EferFlags::NO_EXECUTE_ENABLE` bit in the EFER MSR. Status is logged.`
                *   `SMEP (Supervisor Mode Execution Prevention): Detected by checking bit 20 of EBX from CPUID leaf 0x7, sub-leaf 0. If supported, enabled by setting `Cr4Flags::SUPERVISOR_MODE_EXECUTION_PREVENTION` in CR4. Status is logged.`
                *   `SMAP (Supervisor Mode Access Prevention): Detected by checking bit 21 of EBX from CPUID leaf 0x7, sub-leaf 0. If supported, enabled by setting `Cr4Flags::SUPERVISOR_MODE_ACCESS_PREVENTION` in CR4. Status is logged.`
            *   Future Consideration: Design for leveraging detected CPU features (e.g., AVX, specific SSE extensions) to select optimized code paths for performance-critical kernel routines.
        *   **1.2.6. Timekeeping Setup:**
            *   Initialize PIT (Programmable Interval Timer) for basic timekeeping and scheduler ticks (in `arch` module). HPET (High Precision Event Timer) support is a future enhancement, requiring ACPI parsing to detect and configure. (PIT configured for 100Hz in `kmain`).
                *   Implemented PIT-based sleep/delay: Provided `sleep_ms(ms)` and `sleep_ticks(ticks)` functions in `arch::x86_64::pit` using busy-waiting on the PIT tick counter.
            *   Read initial time from RTC: Implement robust reading of date and time from the CMOS RTC. This includes handling BCD/binary modes, 12/24 hour formats, and ensuring data stability (e.g., checking update-in-progress flags).
                *   Improved RTC year determination: `read_current_time` in `drivers::rtc` now attempts to read CMOS register 0x32 for the century. It applies BCD conversion if needed (assuming same mode as other date/time fields) and includes a plausibility check (19-21). If invalid, it falls back to a 19xx/20xx heuristic based on the 2-digit year.
                *   Ensured NMI safety for RTC access: Individual RTC register reads via `read_rtc_register` in `drivers::rtc` are now wrapped in `interrupts::without_interrupts(|| { ... })` critical sections.
            *   Basic Time of Day Service Implemented:
                *   `Storage: Boot time is stored in a `static spin::Once<DateTime>` in `drivers::rtc.rs`. PIT maintains `SYSTEM_TICKS` and `CONFIGURED_PIT_FREQUENCY` in `arch::x86_64::pit.rs`.'
                *   `Calculation of Current Time: A new function `get_current_wall_clock_time() -> Option<DateTime>` in `drivers::rtc.rs` combines the stored boot time with current uptime (from `pit::get_uptime_seconds()`). It performs date arithmetic to handle rollovers across seconds, minutes, hours, days, months, and years.`
    *   **1.3. Foundational Subsystems (First Pass - as called from `kernel_init` equivalent):**
        *   **1.3.1. Physical Memory Allocator (Frame Allocator):**
            *   Implement the chosen frame allocator (e.g., Bitmap Allocator, Buddy Allocator) in Rust (`mm` module).
            *   Provide robust APIs to allocate and deallocate single and contiguous physical frames.
            *   `Choice of Allocator: Justify the choice (e.g., Bitmap for simplicity in early stages, Buddy for better fragmentation handling if complexity is acceptable).`
            *   `Bitmap Allocator Details (if chosen):`
                *   `Data structure: `Vec<u64>` or `&'static mut [u64]` representing available frames. Bit manipulation for set/clear.`
                *   `Search strategy for free frames (e.g., first-fit).`
                *   `Handling of memory regions: How the allocator manages potentially non-contiguous usable memory regions passed from the pre-allocator (1.2.4). Does it manage multiple bitmaps or one large virtualized bitmap?`
            *   `Buddy Allocator Details (if chosen):`
                *   `Data structures: Free lists for different order blocks (powers of 2).`
                *   `Splitting and coalescing logic.`
                *   `Minimum and maximum block order.`
            *   `API Design:`
                *   `allocate_frame() -> Result<PhysFrame, AllocationError>`
                *   `deallocate_frame(frame: PhysFrame)`
                *   `allocate_contiguous_frames(count: usize) -> Result<PhysFrameRange, AllocationError>` (if supported by chosen allocator).
                *   `Error types: `AllocationError::OutOfMemory`, `AllocationError::ContiguousNotPossible`.`
            *   `Tracking Statistics: Maintain counts of total, free, and used frames.`
            *   `Integration: How this allocator replaces or uses information from the `BootInfoFrameAllocator` (from 1.2.4). Likely, the chosen allocator will take ownership of all usable frames identified by the pre-allocator.`
            *   `Potential Enhancements & Future Considerations for Frame Allocator (Task 1.3.1):`
                *   `Robustness & Error Handling:`
                    *   `Deallocation Safety: Implement strict checks in `deallocate_frame` and `deallocate_contiguous_frames` to `panic!` or log critical errors on attempts to double-free or free an unmanaged/unallocated frame. This is crucial for early bug detection.`
                    *   `Initialization Safety: Add more assertions within the allocator's `init` function (e.g., verifying input region alignment) to catch misuse earlier.`
                *   `Performance Optimizations:`
                    *   `Optimized `allocate_contiguous_frames()`: The current bitmap search for contiguous frames can be improved. Explore methods like using CPU bit manipulation instructions (`trailing_zeros` on u64 words) or more advanced bitmap scanning techniques to speed up finding large free blocks.`
                    *   `Optimized `deallocate_contiguous_frames()`: Override the default trait method to directly clear ranges of bits in the bitmap, which is more efficient than deallocating frame by frame.`
                *   `Feature Enhancements:`
                    *   `Comprehensive Multi-Region Management: To utilize all available physical memory, the global frame allocator system should manage multiple (potentially non-contiguous) usable memory regions. This might involve a top-level allocator managing a list/array of `BitmapFrameAllocator` instances (one per region). This requires careful design if the heap (which uses this frame allocator) is not yet available for dynamic list structures.`
                    *   `Specific Alignment Allocation: Consider adding an `allocate_frame_aligned(alignment: usize)` method to support requests for frames with specific alignment needs greater than the frame size itself (e.g., for device DMA buffers). This is more complex for bitmap allocators.`
                    *   `Reserved Frames/Watermarking: Implement a mechanism to reserve a small pool of frames for critical kernel operations, preventing complete exhaustion by less critical allocations.`
                *   `Alternative Allocator Strategies (Long-term):`
                    *   `Buddy Allocator: For better management of fragmentation and more efficient allocation of various-sized contiguous blocks, consider implementing a buddy allocator as an alternative or replacement for the bitmap allocator in later development phases.`
        *   **1.3.2. Kernel Virtual Memory Management & Page Table Management:**
            *   Rust APIs for managing x86_64 page tables (PML4, PDPT, PD, PT) (in `mm` module, potentially using `x86_64` crate).
            *   Functions to map/unmap physical pages to virtual addresses in kernel space.
            *   Higher-half kernel layout implementation.
            *   Page fault handler refinement: Differentiate kernel vs. potential user-mode faults (in `interrupts` and `mm` modules).
            *   `Page Table Abstraction (`x86_64` crate):`
                *   `Utilize types like `OffsetPageTable`, `MappedPageTable`, `PhysFrame`, `Page`, `PageTableFlags` from the `x86_64` crate.`
                *   `Define a kernel `Mapper` interface that abstracts page table operations.`
            *   `Higher-Half Kernel Layout:`
                *   `Define kernel virtual address space layout (e.g., kernel code/data starting at `0xffffffff80000000`).`
                *   `Mechanism for mapping physical memory (where kernel is loaded) to these higher-half addresses.`
                *   `Ensure bootloader-provided structures (e.g., `BootInfo`, RSDP if ACPI is used later) are accessible at known virtual addresses.`
            *   `Map/Unmap Functions:`
                *   `map_to(page: Page, frame: PhysFrame, flags: PageTableFlags, frame_allocator: &mut dyn FrameAllocator) -> Result<PageTableEntry, MapToError>`
                *   `unmap(page: Page) -> Result<(PhysFrame, PageTableEntry), UnmapError>`
                *   `Recursive page table mapping for accessing page table frames themselves.`
            *   `Page Fault Handler (`pf_handler` in `interrupts`/`arch`):`
                *   `Inputs: Faulting address (CR2), error code.`
                *   `Differentiate kernel vs. user faults based on CS and error code bits (U/S flag).`
                *   `Kernel faults: Should generally be fatal (panic) unless they are for expected reasons (e.g., copy-on-write, demand paging later). Log detailed fault information.`
                *   `User faults (Phase 3+): Will involve checking VMA (Virtual Memory Area) for the process, demand paging, copy-on-write, etc. For now, might just terminate the "process" (if any).`
                *   `Print faulting address, instruction pointer (from stack frame), error code breakdown (Present, Write, User, Reserved Write, Instruction Fetch).`
        *   **1.3.3. Kernel Heap Allocator (`kmalloc` equivalent):**
            *   Implement a flexible kernel heap allocator framework allowing for different allocation strategies (e.g., linked-list, slab) (in `mm` module).
            *   `Heap Allocator Abstraction:`
                *   `The kernel will use the `core::alloc::GlobalAlloc` trait for its heap allocation needs, implemented by a static `#[global_allocator]` instance.`
                *   `This static instance (e.g., a custom `LockedHeapWrapper`) will internally wrap a specific heap allocator implementation (like a linked-list or slab allocator chosen at compile-time or configured early) and ensure thread-safety, typically via a `spin::Mutex`.`
                *   `This design allows the underlying concrete allocator implementation to be selected or switched with minimal changes to the kernel code that uses the heap.`
            *   `Initial Allocator Implementations (Examples):`
                *   `Linked-List Allocator (Simpler): Maintain a linked list of free blocks. Good for initial heap. Details: block header (size, free flag, next pointer), splitting free blocks, coalescing adjacent free blocks on deallocation.`
                *   `Slab Allocator (More Complex, Efficient for fixed-size objects): Define caches for common object sizes. Each cache has slabs (contiguous frames) divided into objects. Free lists within slabs.`
            *   `The `#[global_allocator]` static will be a wrapper struct (e.g., `LockedHeapWrapper`) that ensures thread-safe access to the chosen underlying heap allocation algorithm via a mutex. This wrapper struct will implement the `core::alloc::GlobalAlloc` trait.`
            *   `Initialization (`heap::init(frame_allocator, heap_start_addr, heap_size, chosen_alloc_type)`):`
                *   `Take a region of virtual address space (e.g., a few MB) and map physical frames to it using the frame allocator and page table functions from 1.3.1 & 1.3.2.`
                *   `Initialize the chosen heap allocator with this memory region.`
                *   `The `chosen_alloc_type` (or a similar configuration mechanism like a feature flag) determines which concrete allocator (e.g., LinkedListAllocator, SlabAllocator) is initialized and becomes the active implementation wrapped by the global allocator instance.`
            *   `Alignment: Ensure allocations meet minimum alignment requirements (e.g., `core::mem::align_of::<usize>()`).`
        *   **1.3.4. Scheduler (Rudimentary):**
            *   Define `Task` structure in Rust (PID, state, registers, kernel stack, page tables) (likely in a new `kernel::process` or `tasks` module).
            *   `Scheduler Abstraction for Flexibility:`
                *   `Define a `Scheduler` trait: This trait will abstract core scheduling operations. Example methods could include: `fn pick_next_task(current_task_state: &TaskState) -> Option<Arc<Mutex<Task>>>;`, `fn add_task(task: Arc<Mutex<Task>>);`, `fn remove_task(pid: PID);`, `fn task_yielded(task: Arc<Mutex<Task>>);`, `fn task_set_blocked(task: Arc<Mutex<Task>>);`, `fn task_set_ready(task: Arc<Mutex<Task>>);`. (The exact method signatures and responsibilities to be refined during implementation).`
                *   `Task state management (e.g., run queues, priority lists, sleep queues) will be encapsulated within concrete implementations of this trait.`
            *   `Global Scheduler Instance: Manage the active scheduler via a global static variable, allowing the scheduler to be potentially replaced or augmented. For example: `static KERNEL_SCHEDULER: Mutex<Box<dyn Scheduler + Send + Sync>> = Mutex::new(Box::new(RoundRobinScheduler::new()));`.`
            *   Initial Scheduler Implementation (Round-Robin): Implement a simple round-robin scheduler as the first concrete scheduler, conforming to the `Scheduler` trait defined below.
            *   Context switching routines (assembly part for registers in `arch`, Rust part for logic).
            *   Create the initial "idle" task(s) and the first "init" kernel thread (which will continue kernel initialization here in the `init` module).
            *   `Task Structure (`Task` or `Process`):`
                *   `Fields: PID, `State (Running, Ready, Blocked, Zombie, New)`, `kernel_stack_pointer: VirtAddr`, `page_table_base: PhysAddr` (pointer to PML4), `context: Registers` (struct for saved registers).`
                *   `Consider `parent_pid`, `children: Vec<PID>`, `exit_code` for later.`
            *   `PID Allocation: Simple atomic counter for now.`
            *   `Task Lists: `static mut TASK_LIST: Option<VecDeque<Arc<Mutex<Task>>>>` or similar for ready queue. `Arc<Mutex<Task>>` allows shared, mutable access.`
            *   `Context Switching (`switch_context(&mut old_regs, &new_regs)`):`
                *   `Assembly part: Save all general-purpose registers, segment registers (if needed), FPU/SSE state (lazily later), RIP, RSP, RFLAGS. Restore them for the new task.`
                *   `Update `CR3` if page tables differ.`
                *   `Update TSS `rsp0` if switching from user to kernel or between kernel tasks that might have different kernel stack privileges (less common for simple kernel threads).`
            *   `Scheduler Invocation (`schedule()` function):`
                *   `This core function is invoked by the timer interrupt handler (periodically) or when a task explicitly yields or changes its state (e.g., blocks on I/O, exits).`
                *   `It will interact with the active `KERNEL_SCHEDULER` instance (e.g., `KERNEL_SCHEDULER.lock().pick_next_task(...)`) to determine if a task switch is necessary and which task should run next.`
                *   `If a different task is chosen, `schedule()` will then orchestrate the context switch to that task.`
                *   `This design decouples the decision-making (algorithm via the trait) from the mechanism of scheduling and context switching.`
            *   `Kernel Thread Creation (`create_kernel_thread(entry_point: fn()) -> PID`):`
                *   `Allocate a kernel stack.`
                *   `Initialize the new task's `Registers` context: `RIP` to `entry_point`, `RSP` to top of new kernel stack. Set appropriate RFLAGS (e.g., interrupts enabled).`
                *   `Add to `TASK_LIST` with `State::Ready`.`
            *   `Idle Task: A simple loop (`fn idle_loop() { loop { hlt(); } }`). One per CPU in SMP later.`
            *   `Init Kernel Thread: The first "real" kernel thread (PID 1 for kernel) that continues `kernel_init` or other high-level initializations.`
            *   `Yielding: `fn sched_yield()` that explicitly calls `schedule()` (or sets a flag for timer interrupt to schedule).`
        *   **1.3.5. `initcalls` / Module Initialization Analogue Implemented:**
            *   **Status:** Implemented.
            *   **Location:** `src/init/initcalls.rs` (core logic) and `src/init/mod.rs` (module structure and `kernel_init` orchestrator).
            *   **Design:**
                *   `InitLevel` Enum: Defined with levels from `Early` (0) to `Late` (100) for ordered initialization (e.g., `ArchBasic`, `MemoryManagement`, `DeviceCore`, `Tasks`, `Filesystem`). Ordinal values allow sorting.
                *   `InitError` Enum: Basic error type `Failed(&'static str)` for init functions.
                *   `InitCallFunction` Type: `fn() -> Result<(), InitError>`.
                *   `InitCall` Struct: Contains `level: InitLevel`, `func: InitCallFunction`, and `name: &'static str` for logging.
                *   Storage: `static INIT_CALLS: Mutex<Vec<InitCall>>` in `src/init/initcalls.rs`. This stores all registered initcalls and relies on the heap allocator being available for registrations.
            *   **Registration Mechanism:**
                *   `pub fn register_initcall(level: InitLevel, func: InitCallFunction, name: &'static str)`:
                    *   Located in `src/init/initcalls.rs`.
                    *   Adds a new `InitCall` to the `INIT_CALLS` vector.
                    *   The vector is sorted by `InitLevel` after each addition to maintain order.
            *   **Execution Mechanism:**
                *   `pub fn run_initcalls_for_level(target_level: InitLevel)`:
                    *   Located in `src/init/initcalls.rs`.
                    *   Iterates through the sorted `INIT_CALLS` list.
                    *   Executes functions matching `target_level`.
                    *   Logs execution start, success, or failure (with error details) for each initcall using `kprintln!`.
                    *   Panics if an initcall at a critical level (e.g., `MemoryManagement`, `ArchBasic`, `Interrupts`) returns an error. Continues with logging for non-critical errors.
            *   **Orchestration:**
                *   The main `kernel_init()` function in `src/init/mod.rs` is responsible for orchestrating the boot sequence.
                *   It calls `register_builtin_initcalls()` to register initcalls from various kernel modules.
                *   It then calls `run_initcalls_for_level()` for each `InitLevel` in the prescribed order.
            *   **Integration:**
                *   Called from `kmain()` in `src/main.rs` after very early architecture and console setup.
                *   Example initcalls and registration functions (`init_mm_subsystem`, `register_mm_initcalls`, `init_drivers_subsystem`, `register_drivers_initcalls`) created in `mm` and `drivers` modules to demonstrate usage.
        *   **1.3.6. Refined Console Drivers & Dynamic Manager:** Abstract console output behind a common interface and implement a dynamic console registration system. This task ensures that kernel messages can be routed to multiple output devices (e.g., serial, VGA) through a unified logging interface. This relies on the heap allocator (Task 1.3.3) being initialized before console registration that requires dynamic allocation.
            *   **Prerequisites (Assumed Complete from Prior Work/Initial Setup):**
                *   `KernelConsole` Trait: Defined in `src/drivers/mod.rs` (`pub trait KernelConsole: Send + Sync { fn write_string(&mut self, s: &str); }`). A blanket implementation `impl<T: KernelConsole + ?Sized> core::fmt::Write for T` is also provided.
                *   Serial Port Implementation: `uart_16550::SerialPort` in `src/drivers/serial.rs` implements `KernelConsole`.
            *   **Sub-Task 1: Implement `KernelConsole` for VGA**
                *   Location: `src/drivers/vga_buffer.rs`
                *   Action: Ensure `vga_buffer::Writer` implements the `KernelConsole` trait, particularly the `write_string` method.
            *   **Sub-Task 2: Console Management System**
                *   New File: `src/drivers/console.rs`
                *   `ConsoleFlags` Enum/Bitflags: Define flags like `ENABLED`, `BOOT`.
                    ```rust
                    // Example:
                    // bitflags! {
                    //     pub struct ConsoleFlags: u32 {
                    //         const ENABLED = 1 << 0;
                    //         const BOOT    = 1 << 1;
                    //         // ... other flags as needed
                    //     }
                    // }
                    ```
                *   `ConsoleRegistration` Struct:
                    ```rust
                    // pub struct ConsoleRegistration {
                    //     pub name: &'static str,
                    //     pub driver: &'static spin::Mutex<dyn KernelConsole + Send + Sync>,
                    //     pub flags: ConsoleFlags,
                    //     pub index: u8,
                    // }
                    ```
                *   Console List: `static REGISTERED_CONSOLES: Mutex<Vec<ConsoleRegistration>> = Mutex::new(Vec::new());` (Requires heap).
                *   Registration Functions:
                    *   `pub fn register_console(reg: ConsoleRegistration)`
                    *   `pub fn unregister_console(name: &'static str, index: u8)`
            *   **Sub-Task 3: Update `src/drivers/mod.rs`**
                *   Add `pub mod console;` and `pub use self::console::*;`.
                *   Implement `pub fn init_consoles()`:
                    *   Called after heap allocator initialization.
                    *   Retrieves static `SERIAL1` and `VGA_WRITER` instances (ensuring they are `pub` or accessible).
                    *   Creates `ConsoleRegistration` for them.
                    *   Calls `register_console()` for each.
            *   **Sub-Task 4: Update Logging Macros (`src/logging.rs`)**
                *   Modify `_print` function to use the `REGISTERED_CONSOLES` list:
                    *   Lock `REGISTERED_CONSOLES`.
                    *   Iterate if not empty. For each enabled console:
                        *   Lock its `driver` mutex.
                        *   Call `write_fmt()` (or `write_string()`).
                *   Fallback Mechanism: If `REGISTERED_CONSOLES` is empty or its lock fails (e.g., very early boot/panic), `_print` must fall back to attempting direct writes to `SERIAL1` and `VGA_WRITER` static instances.
            *   **Sub-Task 5: Update `main.rs`**
                *   Call `drivers::init_consoles()` in `kmain()` after heap initialization.
                *   Panic Handler: Should first try `kprintln!`. If it fails (or to be absolutely safe), it must have a hardcoded fallback to write directly to `SERIAL1.lock()` and `VGA_WRITER.lock()`.

2.  **Phase 2: Core Subsystem Initialization & Interdependencies**
    *   This phase transitions the kernel from early setup to a more functional state. It relies heavily on the `initcalls` mechanism established in Phase 1.3.5. The `init::kernel_init()` function will systematically execute registered initcalls for appropriate levels (e.g., `InitLevel::MemoryManagement`, `InitLevel::ArchPostMM`, `InitLevel::DevicePreScheduler`, `InitLevel::Tasks`, `InitLevel::DevicePostScheduler`).
    *   **2.1. Execute `MemoryManagement` Initcalls:**
        *   **Goal:** Bring the main Memory Management (`mm`) module to full operational status, as detailed in `src/mm/mm_dev_plan.md` (Phase 1: Core Kernel Memory Management).
        *   **Action by `init::kernel_init()`:**
            *   Invoke `run_initcalls_for_level(InitLevel::MemoryManagement)`. This will trigger `mm::init()` or a similar top-level MM initcall.
        *   **Expected Key Actions within `mm::init()` (illustrative, see `mm_dev_plan.md` for canonical details):**
            *   **2.1.1. Advanced Physical Frame Allocator Setup:** (Corresponds to `mm_dev_plan.md` Task 1.1)
                *   Initialize the chosen advanced frame allocator (e.g., Buddy Allocator).
                *   Integrate all usable physical memory regions from `BootInfo`.
                *   Mark frames used by early boot/`BootInfoFrameAllocator` (e.g., for initial page tables, ACPI tables if kept) as reserved.
            *   **2.1.2. Kernel Virtual Address Space Finalization:** (Corresponds to `mm_dev_plan.md` Task 1.2)
                *   Solidify kernel's higher-half layout, including Direct Physical Map, `vmalloc` area, MMIO area.
                *   Ensure all necessary kernel sections and bootloader info are correctly mapped.
            *   **2.1.3. Kernel Heap Initialization (`kmalloc`):** (Corresponds to `mm_dev_plan.md` Task 1.3)
                *   Initialize Slab/Buddy backed general-purpose kernel heap.
                *   Set up `#[global_allocator]` for dynamic collections (`Box`, `Vec`, etc.).
            *   **2.1.4. `vmalloc` Area Initialization:** (Corresponds to `mm_dev_plan.md` Task 1.4)
                *   Prepare the `vmalloc` virtual address region and its management structures.
            *   **2.1.5. MMIO Remapping Support (`ioremap`):** (Corresponds to `mm_dev_plan.md` Task 1.5)
                *   Initialize structures for managing the MMIO virtual address region.
            *   **2.1.6. Kernel Page Fault Handler Refinement:** (Corresponds to `mm_dev_plan.md` Task 1.6)
                *   Ensure the kernel page fault handler is robust for faults related to these new MM regions (e.g., `vmalloc` guard pages).
        *   **Dependencies:** `BootInfoFrameAllocator` (Phase 1.2.4), initial paging (Phase 1.1), ACPI tables (Phase 1.3.0 for memory map details).
        *   **Key Outcomes:** Kernel has full dynamic memory allocation (heap, `vmalloc`). Advanced PMM and VMM services are operational.
    *   **2.2. Execute `Arch` Finalization Initcalls (`ArchPostMM`):**
        *   **Goal:** Perform any architecture-specific setup that required full MM services.
        *   **Action by `init::kernel_init()`:**
            *   Invoke `run_initcalls_for_level(InitLevel::ArchPostMM)` (or a similarly named level).
        *   **Expected Actions (Examples):**
            *   Finalize per-CPU data areas if dynamic allocation was needed.
            *   Initialize advanced CPU features (e.g., performance counters, specific MSRs) that might need heap for their management structures.
            *   Further ACPI interactions if ACPI handler parsing needed dynamic memory (e.g., for AML interpretation, device configuration objects).
        *   **Dependencies:** Full MM (Step 2.1).
        *   **Key Outcomes:** Architecture-specific components are fully initialized and optimized.
    *   **2.3. Core Device Initialization (`DevicePreScheduler`):**
        *   **Goal:** Initialize essential device drivers required before the scheduler can effectively run or needed for further system bootstrap (e.g., mounting root FS).
        *   **Action by `init::kernel_init()`:**
            *   Invoke `run_initcalls_for_level(InitLevel::DriversCore)` (or `DevicePreScheduler`).
        *   **Expected Key Driver Initializations:**
            *   **System Timer (High Resolution):** (Refined from Phase 1.2.6)
                *   Initialize HPET if detected via ACPI (from Phase 1.3.0), map its registers (using `mm::ioremap`).
                *   Configure HPET as the primary time source for kernel ticks and high-resolution timers.
                *   If HPET not available, ensure PIT is configured appropriately.
                *   Provide an abstraction layer (`kernel_timer::set_periodic_tick()`, `kernel_timer::get_uptime()`).
            *   **Interrupt Controller (Finalization):** (Refined from Phase 1.2.3)
                *   If full I/O APIC setup was deferred (e.g., waiting for ACPI MADT parsing which might need heap), complete it now.
                *   Enable specific IRQs required by initialized drivers (e.g., timer IRQ).
            *   **Real-Time Clock (RTC) Driver:** Ensure RTC is readable for wall clock time, possibly with better ACPI integration if available.
            *   **(Optional) Early Bus Systems (e.g., PCI):**
                *   Perform initial PCI bus scan if necessary to find critical boot devices (e.g., root disk controller if not using initramfs directly).
                *   Allocate resources (I/O ports, memory regions) for essential PCI devices.
        *   **Dependencies:** Full MM (Step 2.1), ACPI (Phase 1.3.0).
        *   **Key Outcomes:** System timer is providing reliable ticks. Interrupts for core devices are active. Path to discovering more devices is ready.
    *   **2.4. Rudimentary Scheduler Activation (`Tasks` initcall level):**
        *   **Goal:** Initialize and activate the rudimentary scheduler defined in Phase 1.3.4.
        *   **Action by `init::kernel_init()`:**
            *   Invoke `run_initcalls_for_level(InitLevel::Tasks)`. This would call `tasks::scheduler::init_scheduler_subsystem()` (or similar).
        *   **Expected Actions within `scheduler::init_scheduler_subsystem()`:**
            *   Initialize `KERNEL_SCHEDULER` with `RoundRobinScheduler` (or chosen initial scheduler).
            *   Initialize `TASK_REGISTRY`.
            *   Create the per-CPU idle tasks using `create_kernel_thread`. Stack allocated from kernel heap. `Task` struct initialized.
            *   Create the main "init" kernel thread (e.g., `kinit_thread_entry`) using `create_kernel_thread`. This thread will continue executing `kernel_init`'s later phases or a dedicated function.
            *   Set `CURRENT_TASK_PID` to the `kinit_thread`'s PID.
            *   Configure the system timer (from 2.3) to deliver scheduler ticks at the desired frequency (e.g., 100Hz, 1000Hz). The timer IRQ handler will call `scheduler::tick()` which sets `TASK_SWITCH_REQUESTED`.
            *   Perform the *first ever* context switch from the bootstrap context of `kmain` to the `kinit_thread`. This involves setting up a dummy "current task" context representing `kmain` and calling `arch::context_switch()`.
        *   **Dependencies:** Kernel Heap (2.1.3), System Timer (2.3).
        *   **Key Outcomes:** Kernel is now multi-tasking. The `kinit_thread` is running, and idle tasks will run when `kinit_thread` blocks or yields.
    *   **2.5. Further Device Initialization (`DevicePostScheduler`):**
        *   **Goal:** Initialize remaining and non-essential device drivers. These drivers can now assume a scheduler is running and kernel threads can be created.
        *   **Action by `kinit_thread` (continuation of `kernel_init` logic):**
            *   Invoke `run_initcalls_for_level(InitLevel::DriversPlatform)` (or `DevicePostScheduler`).
        *   **Expected Actions (Examples):**
            *   Full PCI device scan and resource allocation.
            *   Storage drivers (IDE, AHCI/SATA, NVMe) if not loaded earlier for initramfs.
            *   USB controllers and core.
            *   Network interface controllers.
            *   GPU console driver (if a more advanced one than early VGA/serial is planned).
            *   Other platform devices.
            *   Drivers may spawn their own kernel threads for interrupt handling (top/bottom halves) or deferred work.
        *   **Dependencies:** Scheduler (2.4), Full MM (2.1), Core Devices (2.3).
        *   **Key Outcomes:** Most system hardware is initialized and available for use by higher-level kernel services or for userspace.

3.  **Phase 3: Filesystem Setup & Early Userspace Preparation**
    *   This phase focuses on initializing the file system infrastructure and mounting the root filesystem. This is a critical step towards preparing for the first user-space process. It typically follows core driver initialization and scheduler activation. This phase would largely be executed by the `kinit_thread`.
    *   **3.1. Virtual File System (VFS) Initialization:**
        *   **Goal:** Initialize the VFS layer, providing a unified interface to different filesystem types.
        *   **Action by `kinit_thread`:**
            *   Invoke `run_initcalls_for_level(InitLevel::Filesystem)`. This would call a top-level `vfs::init_vfs_subsystem()` or similar.
        *   **Expected actions within `vfs::init_vfs_subsystem()`:**
            *   **3.1.1. Initialize Core VFS Data Structures:**
                *   `FILESYSTEM_TYPES: Mutex<HashMap<&'static str, Arc<FilesystemType>>>` (for registered filesystem drivers).
                *   `MOUNT_TABLE: Mutex<Vec<Arc<Mount>>>` (list of active mounts).
                *   `DENTRY_CACHE: Mutex<LRUCache<GlobalDentryId, Arc<Dentry>>>` (global cache for dentry objects).
                *   `INODE_CACHE: Mutex<LRUCache<GlobalInodeId, Arc<Inode>>>` (global cache for inode objects).
                *   Initialize these structures (e.g., create empty HashMaps, initialize LRU caches with defined capacities).
            *   **3.1.2. Define Core VFS Object Structures and Traits (ensure these are well-defined in `src/fs/vfs.rs` or similar):**
                *   `struct FilesystemType { name: &'static str, mount: fn(device_path: Option<&Path>, flags: MountFlags, data: Option<&str>) -> Result<Arc<Superblock>>, fs_flags: FilesystemFlags }`
                *   `struct Mount { superblock: Arc<Superblock>, mount_point_dentry: Arc<Dentry>, mount_flags: MountFlags }`
                *   `struct Superblock { id: SuperblockId, fs_type: Arc<FilesystemType>, root_dentry: Arc<Dentry>, ops: Arc<dyn SuperblockOps>, specific_data: Box<dyn Any + Send + Sync>, ... }`
                *   `struct Inode { id: InodeId (local to FS), global_id: GlobalInodeId, kind: InodeKind, perms: u16, uid: u32, gid: u32, size: u64, nlinks: u32, atime, mtime, ctime, ref_count: AtomicUsize, ops: Arc<dyn InodeOps>, superblock: Weak<Superblock>, specific_data: Box<dyn Any + Send + Sync>, ... }`
                *   `struct Dentry { name: OsString, inode: Option<Arc<Inode>>, parent: Weak<Dentry>, children: Mutex<HashMap<OsString, Arc<Dentry>>>, mount: Option<Arc<Mount>>, ... }` (inode is `None` for negative dentries).
                *   `struct File { dentry: Arc<Dentry>, current_offset: AtomicU64, open_flags: OpenFlags, ops: Arc<dyn FileOps>, private_data: Option<Box<dyn Any + Send + Sync>>, ... }`
                *   `trait SuperblockOps: Send + Sync { fn read_inode(&self, inode_num: u64) -> Result<InodeSpecificData>; fn write_inode(&self, inode: &Inode); ... }`
                *   `trait InodeOps: Send + Sync { fn lookup(&self, parent_inode: &Arc<Inode>, name: &OsStr) -> Result<Arc<Dentry>>; fn create(...); fn mkdir(...); fn readlink(...); ... }`
                *   `trait FileOps: Send + Sync { fn read(...); fn write(...); fn ioctl(...); ... }`
            *   Log VFS initialization: "VFS initialized. Dentry/Inode caches (X entries) ready."
        *   **Dependencies:** Kernel Heap (Phase 2.1).
        *   **Key Outcomes:** VFS layer is ready. Filesystem types can be registered. Core VFS operations are available.
    *   **3.2. Register Embedded Filesystem Drivers (e.g., InitRamFs, TmpFs):**
        *   **Goal:** Make drivers for initial filesystems (especially initramfs) available to VFS.
        *   **Action by `kinit_thread` (or initcalls at `InitLevel::FilesystemDrivers`):**
            *   `initramfs::register_filesystem_type()`: This function would create a static `FilesystemType` for "initramfs" and call `vfs::register_filesystem()`.
            *   `tmpfs::register_filesystem_type()`: Similarly for an in-memory temporary filesystem.
        *   **Expected actions within `initramfs::register_filesystem_type()`:**
            *   The `mount` function for initramfs (`initramfs_mount_fn`) will:
                1.  Take initramfs location/data (e.g., `PhysAddr`, `size` passed via `mount` options or a global var set by bootloader parsing).
                2.  Parse the initramfs archive (e.g., CPIO format): iterate entries, extract metadata (name, mode, size, etc.) and file content.
                3.  For each entry, creating `Inode` and `Dentry` objects. Inodes get unique IDs. Data for file inodes might point directly into the initramfs memory region or be copied.
                4.  Construct the directory tree by linking `Dentry` objects.
                5.  Create and return the `Superblock` for this initramfs instance, with its root `Dentry`.
        *   **Dependencies:** VFS initialized (3.1). Initramfs data available in memory. Kernel Heap.
        *   **Key Outcomes:** "initramfs" and "tmpfs" (if included) filesystem types are registered with VFS.
    *   **3.3. Mount Root Filesystem (`/`) and Core Pseudo-Filesystems:**
        *   **Goal:** Establish the root of the filesystem hierarchy and essential early mounts.
        *   **Action by `kinit_thread`:**
            *   **Mount rootfs:**
                *   `let root_mount_options = MountOptions { device_path: None, data: Some(format!("addr:{:#x},size:{}", initramfs_addr, initramfs_size)) };`
                *   `let root_superblock = vfs::kernel_mount("initramfs", MountFlags::READ_ONLY, root_mount_options)?;`
                *   `vfs::set_kernel_root_mount(root_superblock.root_dentry())?;` (Sets global `KERNEL_ROOT_DENTRY`).
            *   **Mount `/dev` (devtmpfs):**
                *   `let dev_dentry = vfs::kernel_create_dir_at(vfs::get_kernel_root_dentry(), "dev", 0o755)?;`
                *   `let dev_superblock = vfs::kernel_mount("devtmpfs", MountFlags::empty(), MountOptions::empty())?;`
                *   `vfs::kernel_graft_mount(dev_superblock, dev_dentry)?;` (Mounts devtmpfs on `/dev`).
                *   Populate essential device nodes in `/dev` by `devtmpfs` or specific drivers: e.g., `/dev/console`, `/dev/null`, `/dev/zero`.
            *   **(Optional) Mount `/proc` (procfs):**
                *   Similar steps to mount a `procfs` on `/proc` if implemented.
        *   **Dependencies:** VFS (3.1), initramfs/devtmpfs drivers registered (3.2).
        *   **Key Outcomes:** Root filesystem (`/`) is mounted and accessible. `/dev` is mounted. Kernel can perform path lookups.
    *   **3.4. Prepare Execution Context for Init Process:**
        *   **Goal:** Set up CWD, root directory, and standard I/O for the upcoming init process.
        *   **Action by `kinit_thread`:**
            *   Set current working directory and root for this kernel thread (which will become PID 1) to `/`.
                *   `let root_dentry = vfs::get_kernel_root_dentry();`
                *   `process::current().set_cwd(root_dentry.clone());`
                *   `process::current().set_chroot(root_dentry);`
            *   Verify init executable (e.g., `/sbin/init` or `/init` from kernel cmdline).
                *   `let init_path = Path::new(kernel_cmdline.get_init_path().unwrap_or("/sbin/init"));`
                *   `let init_dentry = vfs::kernel_lookup_path(init_path)?;`
                *   Ensure it's a file and executable.
            *   Prepare standard I/O (stdin, stdout, stderr) for the init process:
                *   `let console_path = Path::new("/dev/console");`
                *   `let console_dentry = vfs::kernel_lookup_path(console_path)?;`
                *   `let stdin_file  = vfs::kernel_open_from_dentry(console_dentry.clone(), OpenFlags::READ)?;`
                *   `let stdout_file = vfs::kernel_open_from_dentry(console_dentry.clone(), OpenFlags::WRITE)?;`
                *   `let stderr_file = vfs::kernel_open_from_dentry(console_dentry, OpenFlags::WRITE)?;`
                *   These `Arc<File>` objects would be associated with FDs 0, 1, 2 when the actual process structure for PID 1 is created in Phase 4.
        *   **Dependencies:** Root and `/dev` mounted (3.3). Console device node `/dev/console` exists.
        *   **Key Outcomes:** The kernel environment is prepared for loading and running the first user-space program. Standard I/O paths are resolved.

4.  **Phase 4: Late Kernel Initialization & Userspace Transition**
    *   This phase represents the final steps taken by the main kernel initialization thread (e.g., `kinit_thread` from Phase 2.4). It involves final kernel cleanups, freeing unused boot memory, and finally, launching the first user-space process (PID 1). This is analogous to Linux's `rest_init()` actions and the final stages of `kernel_init` before `execve`.
    *   **4.1. Finalize Asynchronous Initializations & System Quiescence:**
        *   **Goal:** Ensure all critical asynchronous tasks started during earlier initcall levels (especially device driver probing) are complete or have reached a stable point.
        *   **Actions by `kinit_thread`:**
            *   Implement a mechanism to wait for or check the status of major asynchronous initializations if any were launched (e.g., threaded device probing, firmware loading). This might involve checking atomic flags, joining on kernel threads, or polling device readiness.
            *   Ensure critical drivers (storage, console) are confirmed to be operational.
        *   **Dependencies:** All initcall levels up to `DevicePostScheduler` have been processed. Scheduler running. VFS and root FS operational.
        *   **Key Outcomes:** System hardware state is stable and known. All drivers essential for basic operation and userspace launch are confirmed ready.
    *   **4.2. Free Unused Boot-Time Kernel Memory:**
        *   **Goal:** Reclaim memory sections used only during kernel boot and initialization.
        *   **Actions by `kinit_thread`:**
            *   **Identify Reclaimable Kernel Sections:**
                *   Consult linker script symbols (e.g., `__init_begin`, `__init_end`, `__init_data_begin`, `__init_data_end`) to identify the virtual address ranges of `.init.text` and `.init.data` sections.
            *   **Reclaim Kernel `.init` Sections:**
                1.  Verify that the current execution context (`kinit_thread`) is not running from within these `.init` sections.
                2.  Iterate through the virtual page ranges covered by `.init.text` and `.init.data`.
                3.  For each page, call `mm::unmap_kernel_page()` (or a VMM equivalent) to remove its mapping from the kernel page tables. This returns the `PhysFrame`.
                4.  Call `pmm::deallocate_frame()` for each `PhysFrame` obtained.
                5.  Log amount of reclaimed kernel init memory.
            *   **Reclaim Bootloader-Marked Reclaimable Memory:**
                *   Iterate `BootInfo.memory_regions`. For regions of kind `BootloaderReclaimable`:
                    1.  Ensure these regions are not vital (e.g., ACPI tables might be here if not copied). Best practice is to copy necessary bootloader data to kernel heap earlier.
                    2.  Convert physical addresses to `PhysFrame`s.
                    3.  Call `pmm::deallocate_frame()` for each frame in these regions.
                    4.  Log amount of reclaimed bootloader memory.
        *   **Dependencies:** Full MM services (PMM, VMM) operational (Phase 2.1). All code and data within the `.init` sections are no longer needed.
        *   **Key Outcomes:** Increased available physical memory. Kernel's resident memory footprint is reduced.
    *   **4.3. Create and Prepare PID 1 (User-Space `init` Process):**
        *   **Goal:** Create the first user-space process (PID 1), load its executable (`/sbin/init` or similar), and set up its initial execution environment.
        *   **Actions by `kinit_thread`:**
            *   **Define Path to Init Executable:** Determine path (e.g., `"/sbin/init"`, `"/init"`) from kernel command line options (`init=...`) or a compiled-in default.
            *   **Create User Process Context:**
                *   Call a function like `process_manager::create_first_user_process(init_exe_path: &Path) -> Result<ProcessId, ProcessError>`. This function encapsulates:
                    1.  Assigning PID 1.
                    2.  Creating a new `AddressSpace` for PID 1 using `mm::AddressSpace::create()`.
                    3.  Loading the init executable (e.g., `/sbin/init`) into this new address space using `AddressSpace::load_executable(init_exe_path)`. This involves:
                        *   Opening the executable file via VFS.
                        *   Parsing the executable format (e.g., ELF).
                        *   Creating VMAs for text, data, BSS segments.
                        *   Setting up page table entries for demand paging from the executable.
                        *   Allocating and mapping the initial user stack VMA.
                        *   Determining the user-mode entry point address and initial stack pointer.
                    4.  Initializing the `Process` struct fields: PID, parent (none or kernel), state (`ReadyToRunUser`), credentials (root), signal handlers (defaults), empty file descriptor table.
                    5.  Setting the new process's CWD and root to `/` (using `Dentry` objects obtained from VFS).
            *   **Setup Standard I/O for PID 1:**
                *   Using the `Arc<Process>` for PID 1:
                    *   Open `/dev/console` (or equivalent) via VFS for reading (stdin). Store the resulting `Arc<File>` in PID 1's file descriptor table at FD 0.
                    *   Open `/dev/console` for writing (stdout). Store at FD 1.
                    *   Open `/dev/console` for writing (stderr). Store at FD 2.
            *   **Prepare Kernel Stack for Transition:** The `kinit_thread` itself will become PID 1. Ensure its kernel stack is appropriately sized and prepared for handling system calls from this crucial process.
        *   **Dependencies:** MM capable of creating and managing user address spaces (`mm_dev_plan.md` Phase 3). VFS and root FS operational (Phase 3). Process management structures defined. `/dev/console` available.
        *   **Key Outcomes:** PID 1 process structure exists, its memory space is initialized with the `init` executable, and standard I/O is connected to the console. It's ready to be scheduled and run in user mode.
    *   **4.4. Transition to User Mode and Execute PID 1:**
        *   **Goal:** Transfer CPU control from the kernel (`kinit_thread`) to the user-space `init` process (PID 1).
        *   **Actions by `kinit_thread` (which is about to become PID 1):**
            *   **Final Preparations:**
                *   Set `CURRENT_TASK_PID` (or equivalent global scheduler variable) to PID 1.
                *   Update PID 1's state in `TASK_REGISTRY` to `RunningUserMode`.
            *   **Construct User Mode Trap Frame:**
                *   Populate an architecture-specific trap/interrupt frame on the kernel stack of `kinit_thread`. This frame must contain all register values required to start user-mode execution:
                    *   User-mode instruction pointer (entry point of `/sbin/init`).
                    *   User-mode stack pointer (top of PID 1's user stack).
                    *   User-mode segment selectors (CS, DS, ES, SS - e.g., `USER_CODE_SEG`, `USER_DATA_SEG` on x86_64).
                    *   User-mode CPU flags (e.g., `RFLAGS` with interrupts enabled for user mode).
                    *   Ensure `argc`, `argv`, `envp` are correctly placed on the user stack if the ABI demands this (or passed via registers). Often, for PID 1, `argv` might be `["/sbin/init", NULL]` and `envp` might be minimal (e.g., `["HOME=/", "TERM=linux", NULL]`).
            *   **Perform Architecture-Specific Transition:**
                *   Execute the instruction that switches to user mode and jumps to the user-mode entry point (e.g., `iretq` on x86_64, `eret` on AArch64).
                *   **This is a point of no return for `kinit_thread`'s kernel execution path.**
        *   **Dependencies:** PID 1 process context fully prepared (4.3). Architecture-specific context switching and user-mode transition mechanisms are flawless. Interrupts are enabled (or will be upon `iretq`).
        *   **Key Outcomes:** The CPU is now executing user-space code for PID 1. The kernel's role shifts to being reactive (handling syscalls, interrupts, exceptions).
    *   **4.5. Post-Init Kernel State (Idle Loop, Other Kernel Threads):**
        *   **Goal:** Ensure the rest of the kernel (besides the now user-mode PID 1) continues to function.
        *   **State:**
            *   **Idle Task(s):** The per-CPU idle tasks (created in Phase 2.4) will run whenever PID 1 (or any other future process/thread) is not runnable (e.g., blocked on I/O).
                *   The idle loop: `loop { arch::cpu_idl(); /* e.g. hlt */ scheduler::yield_if_needed(); /* or just rely on timer tick */ }`.
                *   May perform low-priority cleanup or power management.
            *   **(Optional) `kthreadd`-like Kernel Thread (PID 2):**
                *   If Linux's `rest_init` model is followed closely, `rest_init` (called by `kernel_init`) spawns two threads: one becomes user-PID 1, the other becomes `kthreadd` (PID 2).
                *   If `kthreadd` exists, it would be responsible for creating other kernel threads on behalf of kernel subsystems (e.g., for device drivers, background tasks like flushing dirty pages).
                *   If not implemented initially, kernel threads are created ad-hoc by the subsystems needing them (as done for idle/init tasks so far).
            *   Any other primordial kernel threads started earlier continue their execution as scheduled.
        *   **Dependencies:** Scheduler fully operational (Phase 2.4), PID 1 successfully launched (4.4).
        *   **Key Outcomes:** The system is now "live" with both user-space and kernel-space activities. The kernel idles when no work is available and responds to events.

## Integration Points

*   **All other kernel modules (`src/arch`, `src/mm`, `src/fs`, `src/drivers`, `src/interrupts`):** The `init` module calls into almost all other modules to initialize them.
*   **Kernel Core (`src/lib.rs`):** The `_start` function (which calls `kmain`) will likely call into the main initialization function (`kernel_init()`) within this module.
*   **Bootloader Interface:** Receives information like memory map and command-line arguments.
*   **Architecture Specifics (`src/arch`):** Relies on `arch` for early CPU setup before `init` takes over major coordination.
*   **Memory Management (`src/mm`):** Coordinates the full setup of memory management.
*   **File Systems (`src/fs`):** Manages mounting of the root and other initial filesystems.
*   **Device Drivers (`src/drivers`):** Orchestrates the initialization of device drivers.
*   **Interrupts (`src/interrupts`):** Coordinates enabling and setup of interrupt handlers.
