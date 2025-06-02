# Memory Management (`mm`) Development Plan

## Overview

This document details the development plan for the Memory Management (mm) subsystem of GritOS. This module is located at `src/mm`. This is a critical component for managing the system's memory resources.

## Responsibilities

*   Physical memory management (frame allocation).
*   Virtual memory management (paging, address spaces).
*   Kernel memory allocation (slab allocator or similar).
*   Handling memory-related aspects of process creation and management.

## Key C Components (Linux Inspiration - for translation to Rust)

*   `mm/memblock.c` or `memblock`: Boot-time memory allocator. (Researched)
*   `mm/page_alloc.c`: Core page allocator (buddy system).
*   `mm/slab.c` or `mm/slub.c`: Object allocators.
*   `mm/memory.c`: Page fault handling, virtual memory areas (VMAs).
*   `arch/x86/mm/init.c`: Architecture-specific MMU interactions, early paging setup. (Researched)
*   `arch/x86/boot/compressed/head_64.S`: Very early paging setup.

## Rust Implementation Strategy

*   **Data Structures:**
    *   `FrameAllocator`: Trait and implementations (e.g., bitmap or buddy allocator for physical frames).
    *   `PageTable`: Structures for managing page tables (e.g., x86_64 4-level paging, using `x86_64` crate).
    *   `AddressSpace`: Represents a virtual address space.
    *   `KernelAllocator`: A global allocator for kernel data structures (likely based on `slab_allocator` crate or custom).
*   **Logic Flow:**
    *   Initialize physical memory manager early using information from bootloader.
    *   Set up kernel page tables and enable paging.
*   **Crates:** Consider using crates like `x86_64` for page table structures, `linked_list_allocator` or `slab_allocator` for heap.

## Architecture-Specific Considerations

The memory management (MM) subsystem must work closely with the `arch` module to handle hardware-dependent details:

*   **MMU Operations:** All direct interactions with the Memory Management Unit (MMU), such as page table walks, updates to page table registers (e.g., CR3 on x86_64, TTBR0/TTBR1 on AArch64), and TLB (Translation Lookaside Buffer) management, will be delegated to functions or traits provided by the `src/arch/[architecture]/memory.rs` module.
*   **Page Table Structures:** The `mm` module will define generic concepts like virtual and physical addresses, but the actual structure and format of page table entries (PTEs) and the number of paging levels are architecture-specific. The `arch` module will be responsible for managing these specific formats (e.g., using the `x86_64` crate's page table types for x86_64, or custom structures for AArch64).
*   **Memory Layout:** The overall memory layout, including the kernel's virtual address space, physical memory mapping, and regions for memory-mapped I/O, will be defined and managed in coordination with the `arch` module.
*   **Cache Management:** Operations related to CPU cache coherency (e.g., flushing caches after certain memory operations) are architecture-specific and will be handled by the `arch` module if required.
*   **Boot-time Memory Information:** The method for obtaining the initial physical memory map (e.g., from bootloader info like UEFI memory map or E820) will be provided by the `arch`-specific boot code and passed to the `mm` module.

## Development Phases

---

### Phase 1: Boot-time Physical Memory Management (Frame Allocator)

**Goal:** Implement a mechanism to discover and manage available physical memory ranges provided by the bootloader. This allocator will be responsible for tracking physical memory regions (available, reserved, etc.) and providing basic frame allocation/deallocation capabilities for early kernel initialization before a more advanced page allocator (like a buddy allocator) and the kernel heap are available. This is analogous to Linux's `memblock` allocator.

**Key Modules/Files Involved:**

*   `src/mm/mod.rs`
*   `src/mm/types.rs` (or `physical_memory.rs`, for `PhysAddr`, `Frame` etc.)
*   `src/mm/frame_alloc/mod.rs` (for `FrameAllocator` trait)
*   `src/mm/frame_alloc/boot_info_allocator.rs` (main implementation)
*   `src/mm/frame_alloc/region.rs` (for `PhysicalMemoryRegion` and related enums/flags)
*   `src/arch/[target_arch]/mod.rs` or `linker.ld` (for kernel image symbols)
*   `src/panic.rs` or `main.rs` (for panic handler integration)

**1.1. Define Core Physical Memory Primitives and Region Descriptors:**
    *   **Location:** `src/mm/types.rs` or `src/mm/physical_memory.rs`
    *   **Sub-Task 1.1.1:** Define `PhysAddr(u64)`: Represents a physical memory address. Implement `Add`, `Sub`, alignment methods (`align_up`, `align_down`).
    *   **Sub-Task 1.1.2:** Define `Frame`: Represents a physical page frame.
        *   `start_address: PhysAddr`
        *   Methods: `containing_address(addr: PhysAddr) -> Frame`, `number() -> u64` (optional), `size() -> u64` (usually `PAGE_SIZE`).
    *   **Sub-Task 1.1.3:** Define `PAGE_SIZE: u64` constant (e.g., 4096).
    *   **Location:** `src/mm/frame_alloc/region.rs`
    *   **Sub-Task 1.1.4:** Define `MemoryRegionKind` enum: Based on `bootloader_api::info::MemoryRegionKind` (e.g., `Usable`, `Reserved`, `AcpiReclaimable`, `Bootloader`, `KernelPersistent`, `UnknownUefi`, `Unusable`).
    *   **Sub-Task 1.1.5:** Define `MemblockRegionFlags` bitflags struct (initially minimal, consider future needs):
        *   `NOMAP`: (Future) This region should not be mapped in the direct physical map.
        *   `HOTPLUG`: (Future) This region is part of hotpluggable memory.
        *   `MIRROR`: (Future) Mirrored memory.
    *   **Sub-Task 1.1.6:** Define `PhysicalMemoryRegion` struct:
        *   `base: PhysAddr`
        *   `size: u64`
        *   `kind: MemoryRegionKind`
        *   `flags: MemblockRegionFlags`
        *   Methods: `end_address(&self) -> PhysAddr`, `overlaps(&self, other_base: PhysAddr, other_size: u64) -> bool`.

**1.2. Implement `BootInfoFrameAllocator` (or similar named boot-time PMM):**
    *   **Location:** `src/mm/frame_alloc/boot_info_allocator.rs`
    *   **Sub-Task 1.2.1:** Define `BootInfoFrameAllocator` struct:
        *   `memory_map: Vec<PhysicalMemoryRegion>`: Stores all memory regions from bootloader, sorted and merged. These are the *available* physical memory segments.
        *   `reserved_map: Vec<PhysicalMemoryRegion>`: Stores regions explicitly reserved by this allocator (e.g., for allocations or unmovable kernel parts). Sorted and merged.
        *   `boot_info_original_map: &'static bootloader_api::info::MemoryRegions`: Optional, for reference or re-parsing if needed for advanced features.
        *   `current_limit: PhysAddr`: Max address for allocations (e.g., `MEMBLOCK_ALLOC_ACCESSIBLE` concept).
        *   `bottom_up_allocation: bool`: Allocation direction (default: `false` for top-down).
    *   **Sub-Task 1.2.2:** Implement `BootInfoFrameAllocator::new(boot_info: &'static bootloader_api::info::BootInfo) -> Self`:
        *   Iterate `boot_info.memory_regions.iter()`.
        *   Convert each `bootloader_api::info::MemoryRegion` to `gritos_mm::PhysicalMemoryRegion`.
        *   Store these in `memory_map`.
        *   Sort `memory_map` by `base` address.
        *   Merge adjacent regions in `memory_map` if they are contiguous and have compatible `kind` and `flags` (initially, all usable regions might be merged, and all reserved of a specific type).
        *   Initialize `reserved_map` as empty.
        *   Set `current_limit` (e.g., highest address in usable RAM or a specific architecture limit like 4GB/MAX_LOW_PFN initially for simplicity).
        *   Log the initial memory map (total RAM, number of regions) using an early logger if available.
    *   **Sub-Task 1.2.3:** Implement `BootInfoFrameAllocator::add_memory_region(&mut self, base: PhysAddr, size: u64, kind: MemoryRegionKind, flags: MemblockRegionFlags)`:
        *   (Analogous to `memblock_add`).
        *   Creates a new `PhysicalMemoryRegion`.
        *   Inserts it into `memory_map` while maintaining sort order by `base`.
        *   Merges with adjacent regions if compatible.
    *   **Sub-Task 1.2.4:** Implement `BootInfoFrameAllocator::reserve_region(&mut self, base: PhysAddr, size: u64)`:
        *   (Analogous to `memblock_reserve`).
        *   Creates a new `PhysicalMemoryRegion` (kind might be `Reserved` or a specific "KernelReserved").
        *   Inserts it into `reserved_map`, maintaining sort order and merging overlaps. This effectively marks this range as "not available for general allocation by `allocate_frames`".
        *   This function is crucial for protecting the kernel image, initrd, bootloader structures, and any memory already allocated by this allocator.
    *   **Sub-Task 1.2.5:** Implement `BootInfoFrameAllocator::allocate_raw(&mut self, layout: core::alloc::Layout, min_addr: PhysAddr, max_addr: PhysAddr, kind_filter: Option<MemoryRegionKind>, flags_filter: Option<MemblockRegionFlags>) -> Result<PhysAddr, AllocationError>`:
        *   (Core allocation logic, inspired by `memblock_find_in_range_node`).
        *   Determine allocation range: `alloc_start = max(min_addr, PAGE_SIZE)`, `alloc_end = min(max_addr, self.current_limit)`.
        *   Iterate through `self.memory_map` (respecting `bottom_up_allocation` direction).
            *   For each `region` in `memory_map`:
                *   Skip if `region.kind` doesn't match `kind_filter` (typically `Usable`).
                *   Skip if `region.flags` don't match `flags_filter` (if provided).
                *   Calculate actual iteration bounds within this `region` based on `alloc_start`, `alloc_end`.
                *   Iterate candidate addresses within these bounds, stepping by `layout.align()`:
                    *   `candidate_base = align_up(current_search_addr, layout.align())`.
                    *   If `candidate_base + layout.size() > region_end_or_alloc_end`, break from inner loop or adjust.
                    *   Check if `[candidate_base, candidate_base + layout.size())` overlaps with any region in `self.reserved_map`.
                    *   If free:
                        *   Call `self.reserve_region(candidate_base, layout.size())`.
                        *   Return `Ok(candidate_base)`.
        *   Return `Err(AllocationError::NoSuitableRegion)` or `NoMemory`.
    *   **Sub-Task 1.2.6:** Implement `BootInfoFrameAllocator::free_region(&mut self, base: PhysAddr, size: u64)`:
        *   (Analogous to `memblock_phys_free`).
        *   Removes the range `[base, base + size)` from `self.reserved_map`.
        *   This involves finding the overlapping reserved region(s), potentially splitting them or adjusting their bounds. The `memory_map` (available physical memory) is NOT changed by this; freeing only makes a previously reserved area available again to `allocate_raw`.
    *   **Sub-Task 1.2.7:** Define `AllocationError` enum: `NoSuitableRegion`, `NoMemory`, `AlignmentError`.
    *   **Sub-Task 1.2.8:** (Optional) Implement `BootInfoFrameAllocator::dump_regions(&self)` for debugging (prints `memory_map` and `reserved_map`).

**1.3. Define `FrameAllocator` Trait and Global Instance:**
    *   **Location:** `src/mm/frame_alloc/mod.rs`
    *   **Sub-Task 1.3.1:** Define `FrameAllocator` trait:
        ```rust
        pub trait FrameAllocator {
            // Allocates 'count' contiguous frames.
            fn allocate_frames(&mut self, count: usize) -> Result<Frame, AllocationError>;
            // Deallocates 'count' contiguous frames starting at 'frame'.
            fn deallocate_frames(&mut self, frame: Frame, count: usize);

            // Optional, more specific variants:
            // fn allocate_frame_aligned(&mut self, layout: core::alloc::Layout) -> Result<Frame, AllocationError>;
            // fn allocate_frame_in_range(&mut self, min_addr: PhysAddr, max_addr: PhysAddr) -> Result<Frame, AllocationError>;
        }
        ```
    *   **Sub-Task 1.3.2:** Implement `FrameAllocator` for `BootInfoFrameAllocator` (in `boot_info_allocator.rs`):
        *   `allocate_frames` will call `self.allocate_raw` with `layout = Layout::from_size_align(count * PAGE_SIZE, PAGE_SIZE).unwrap()`, appropriate range (e.g., 0 to `self.current_limit`), and `kind_filter = Some(MemoryRegionKind::Usable)`.
        *   `deallocate_frames` will call `self.free_region`.
    *   **Sub-Task 1.3.3:** Create a global static instance for the early allocator:
        *   `static EARLY_PHYSICAL_ALLOCATOR: spin::Mutex<Option<BootInfoFrameAllocator>> = spin::Mutex::new(None);`
    *   **Sub-Task 1.3.4:** Implement `pub fn init_early_physical_allocator(boot_info: &'static bootloader_api::info::BootInfo)`:
        *   Initializes `BootInfoFrameAllocator::new(boot_info)`.
        *   Places it into `EARLY_PHYSICAL_ALLOCATOR`.
        *   This function is called once from `kmain` or very early `init`.
    *   **Sub-Task 1.3.5:** Implement public helper functions for global access:
        *   `pub fn allocate_early_physical_frames(count: usize) -> Result<Frame, AllocationError>` (locks mutex, calls allocator).
        *   `pub fn deallocate_early_physical_frames(frame: Frame, count: usize)` (locks mutex, calls allocator).
        *   `pub fn reserve_early_physical_region(base: PhysAddr, size: u64)` (locks mutex, calls allocator's `reserve_region`).

**1.4. Reserve Critical Early Regions:**
    *   **Location:** `kmain` or early `init` (after `init_early_physical_allocator` call).
    *   **Sub-Task 1.4.1:** Reserve Kernel Image:
        *   Define linker symbols (e.g., `kernel_physical_start`, `kernel_physical_end`) that provide the physical start and end addresses of the loaded kernel image. These must be accessible to Rust code.
        *   Call `reserve_early_physical_region(PhysAddr::new(kernel_physical_start), kernel_physical_end - kernel_physical_start)`.
    *   **Sub-Task 1.4.2:** Reserve Bootloader Information Structures:
        *   If the `BootInfo` structure and its associated data (memory map, initrd) are located in memory that needs to be protected, reserve these regions as well. The physical address and size of `BootInfo` itself would be needed.
    *   **Sub-Task 1.4.3:** Reserve any other known early regions (e.g., ACPI tables if their location is known and they need to be preserved before being parsed and copied).

**1.5. Integration with Panic Handler:**
    *   **Location:** `src/panic.rs` or where the panic handler is defined.
    *   **Sub-Task 1.5.1:** In the panic handler, if `EARLY_PHYSICAL_ALLOCATOR` is initialized, attempt to lock it and call its `dump_regions()` method to print memory layout for diagnostics. This must be done carefully as the system is unstable.

---

### Phase 2: Basic Kernel Paging Setup

**Goal:** Establish the initial kernel address space by creating kernel page tables. This involves mapping the kernel's code and data segments into virtual memory (typically higher-half), setting up a direct physical memory map, and enabling paging. This phase relies on the boot-time frame allocator from Phase 1 for allocating page table frames.

**Key Modules/Files Involved:**

*   `src/mm/paging/mod.rs` (or `src/mm/vmm.rs`)
*   `src/mm/paging/mapper.rs` (for `OffsetPageTable` or similar abstraction)
*   `src/mm/paging/entry.rs` (for page table entry flags, if not using a crate's)
*   `src/arch/[target_arch]/mm/paging.rs` (for arch-specifics like CR3 loading)
*   `src/arch/[target_arch]/linker.ld` (for kernel layout symbols: `_kernel_text_start_phys`, `_kernel_text_end_phys`, `_kernel_rodata_start_phys`, etc., and virtual address constants like `KERNEL_VIRTUAL_OFFSET`, `DIRECT_MAP_VIRTUAL_OFFSET`).
*   `src/mm/frame_alloc/boot_info_allocator.rs` (to implement `x86_64::structures::paging::FrameAllocator`)

**Assumptions for x86_64:**

*   Using the `x86_64` crate for its page table structures (`PageTable`, `Pml4Entry`, etc.), address types (`PhysAddr`, `VirtAddr`), and potentially the `OffsetPageTable` mapper.
*   Targeting 4-level paging (PML4, PDPT, PD, PT).

**Sub-Tasks:**

**2.1. Define Kernel Virtual Address Space Layout:**
    *   **Location:** `src/arch/[target_arch]/memory_layout.rs` or `src/mm/config.rs`
    *   **Sub-Task 2.1.1:** Define `KERNEL_VIRTUAL_OFFSET: u64`. This is the starting virtual address for the kernel's higher-half mapping (e.g., `0xFFFF_8000_0000_0000` or `0xFFFF_FFFF_8000_0000` on x86_64, depending on 4/5-level paging and sign extension).
    *   **Sub-Task 2.1.2:** Define `DIRECT_MAP_VIRTUAL_OFFSET: u64`. The starting virtual address for the direct physical memory map. This must be distinct from the kernel image mapping and other regions.
    *   **Sub-Task 2.1.3:** Define linker symbols for physical and virtual start/end of kernel sections (`.text`, `.rodata`, `.data`, `.bss`). These need to be exposed to Rust code (e.g., via an `extern "C"` block reading from `linker.ld` values).
        *   Example symbols needed: `kernel_text_physical_start`, `kernel_text_virtual_start`, `kernel_text_size`. Similarly for `.rodata`, `.data`, `.bss`.

**2.2. Adapt Boot-Time Frame Allocator for `x86_64` Crate:**
    *   **Location:** `src/mm/frame_alloc/boot_info_allocator.rs`
    *   **Sub-Task 2.2.1:** Implement the `x86_64::structures::paging::FrameAllocator<Size4KiB>` trait for `BootInfoFrameAllocator`.
        *   The `fn allocate_frame(&mut self) -> Option<PhysFrame<Size4KiB>>` method should:
            *   Call the internal `allocate_raw` or `allocate_frames(1)` method of `BootInfoFrameAllocator` to get one `PAGE_SIZE` frame.
            *   Convert the resulting `gritos_mm::Frame` or `PhysAddr` to an `x86_64::structures::paging::PhysFrame<Size4KiB>`.
            *   Return `Some(phys_frame)` on success, `None` on failure.
    *   **Sub-Task 2.2.2:** Ensure `BootInfoFrameAllocator` can deallocate frames if the `x86_64` mapper needs it (though typically for initial setup, deallocation isn't heavily used by the mapper itself).

**2.3. Implement Kernel Page Table Creation Logic:**
    *   **Location:** `src/mm/paging/setup.rs` (or `src/mm/vmm.rs`)
    *   **Sub-Task 2.3.1:** Define `pub fn create_kernel_address_space(boot_info: &'static bootloader_api::info::BootInfo, frame_allocator: &mut BootInfoFrameAllocator) -> Result<KernelMappings, MappingError>`
        *   `KernelMappings` struct could return `active_pml4_frame: PhysFrame`, and `mapper: OffsetPageTable<'static>` (or a custom mapper).
        *   `MappingError` enum: `FrameAllocationFailed`, `PageTableUpdateFailed`.
    *   **Sub-Task 2.3.2:** Allocate PML4 Table:
        *   Use `frame_allocator.allocate_frame()` to get a frame for the PML4 table. This frame must be page-aligned and zeroed.
        *   Let `active_pml4_frame` be this frame.
    *   **Sub-Task 2.3.3:** Initialize `OffsetPageTable` (or custom mapper):
        *   `let pml4_table_ptr = phys_to_virt(active_pml4_frame.start_address(), KERNEL_VIRTUAL_OFFSET or DIRECT_MAP_VIRTUAL_OFFSET)` - The virtual address used to access the PML4 table itself needs to be valid once paging is enabled. This implies part of the direct map or kernel mapping must cover where the PML4 phys frame is. A common technique is recursive mapping of the PML4 into itself, or ensuring the direct map covers it. For `OffsetPageTable`, the `recursive_index` option handles this, or it assumes page tables are part of the higher-half mapping.
        *   `let mut mapper = unsafe { OffsetPageTable::new(&mut *(pml4_table_ptr as *mut PageTable), VirtAddr::new(KERNEL_VIRTUAL_OFFSET)) };`
        *   (Alternative: if not using `OffsetPageTable`'s higher-half assumption for page table access, a temporary mapping might be needed to access the PML4 table initially).
    *   **Sub-Task 2.3.4:** Map Kernel Sections:
        *   Iterate through kernel sections (text, rodata, data, bss) using linker symbols.
        *   For each section:
            *   Determine physical start/end and virtual start/end.
            *   Determine page table flags:
                *   `.text`: Present, Global (if PGE enabled), Executable.
                *   `.rodata`: Present, Global, Read-only (Write-Protect), NoExecute.
                *   `.data`: Present, Global, Writable, NoExecute.
                *   `.bss`: Present, Global, Writable, NoExecute. (BSS needs to be zeroed, which should happen before or during mapping).
            *   For each page in the section:
                *   `let page = Page::containing_address(VirtAddr::new(virtual_addr));`
                *   `let frame = PhysFrame::containing_address(PhysAddr::new(physical_addr));`
                *   `mapper.map_to(page, frame, flags, frame_allocator).unwrap().flush();` (Handle error properly).
    *   **Sub-Task 2.3.5:** Map Early Hardware:
        *   VGA Buffer: Map physical `0xb8000` to a virtual address (e.g., `KERNEL_VIRTUAL_OFFSET + 0xb8000` or a dedicated MMIO virtual range). Flags: Present, Writable, NoExecute.
        *   Map any other MMIO regions needed very early (e.g., serial port if MMIO based, PIC/APIC registers if memory-mapped and needed before full driver init).
    *   **Sub-Task 2.3.6:** Map Bootloader Information:
        *   Map the `BootInfo` structure and its referenced data (memory map, initrd) to known virtual addresses if they need to be accessed after paging is enabled. This might involve iterating `boot_info.memory_regions` and mapping those marked as `Bootloader` or `KernelPersistent`.
    *   **Sub-Task 2.3.7:** (Crucial) Setup Direct Physical Memory Map:
        *   Iterate through all `Usable` physical memory regions identified by `frame_allocator` (or `boot_info.memory_regions`).
        *   For each physical frame `phys_frame` in these usable regions:
            *   `let page = Page::containing_address(VirtAddr::new(DIRECT_MAP_VIRTUAL_OFFSET + phys_frame.start_address().as_u64()));`
            *   Map `page` to `phys_frame` using `mapper.map_to()` with flags: Present, Writable, NoExecute, Global.
            *   **Optimization:** Use large pages (2MB or 1GB) where possible. This requires checking alignment of physical regions and available CPU features (PSE, 1GB pages). The `mapper` might have specific methods for large pages (e.g., `map_to_2mb`, `map_to_1gb`) or infer from alignment.
    *   **Sub-Task 2.3.8:** Return `Ok(KernelMappings { active_pml4_frame, mapper })`.

**2.4. Enable Paging and Finalize:**
    *   **Location:** `src/arch/[target_arch]/mm/paging.rs` or an `init` function.
    *   **Sub-Task 2.4.1:** Implement `pub unsafe fn enable_paging_and_switch(kernel_mappings: KernelMappings)`:
        *   Load the physical address of the PML4 table into CR3:
            `x86_64::registers::control::Cr3::write(kernel_mappings.active_pml4_frame, Cr3Flags::empty());`
        *   Enable Paging (PG bit in CR0) and Write Protection (WP bit in CR0):
            `x86_64::registers::control::Cr0::update(|cr0| { cr0.insert(Cr0Flags::PAGING | Cr0Flags::WRITE_PROTECT); });`
        *   Enable global pages if supported and used (PGE bit in CR4):
            `x86_64::registers::control::Cr4::update(|cr4| { cr4.insert(Cr4Flags::PAGE_GLOBAL_ENABLE); });` (Only if `_PAGE_GLOBAL` was used).
        *   Enable NXE (No-Execute Enable) in EFER MSR if not already done by `init`'s CPU feature detection.
            `x86_64::registers::msr::Efer::update(|efer| { efer.insert(EferFlags::NO_EXECUTE_ENABLE); });`
        *   (Important) The `OffsetPageTable` instance (now `kernel_mappings.mapper`) needs to be stored in a global static variable so the kernel can use it for further page table manipulations.
            `static KERNEL_MAPPER: spin::Mutex<Option<OffsetPageTable<'static>>> = spin::Mutex::new(None);`
            Initialize this static with `kernel_mappings.mapper`.
    *   **Sub-Task 2.4.2:** Post-Paging Adjustments:
        *   Any code that runs immediately after this (e.g., in `kmain`) must now use virtual addresses for all memory access.
        *   Pointers to hardware (like VGA buffer) should be updated to their new virtual addresses.
        *   The stack pointer `RSP` should be valid in the new virtual address space. This is usually handled because the kernel stack itself is part of the mapped kernel image.

**2.5. Define Basic Page Fault Handler for Kernel:**
    *   **Location:** `src/interrupts/exceptions.rs` or `src/arch/[target_arch]/interrupts/exceptions.rs`
    *   **Sub-Task 2.5.1:** Create a very basic page fault handler (`kernel_page_fault_handler(stack_frame: &InterruptStackFrame, error_code: PageFaultErrorCode)`).
        *   This handler is for faults *within kernel mode* after paging is enabled.
        *   Initially, it should:
            *   Read CR2 (faulting address).
            *   Print fault information (address, error code, instruction pointer from `stack_frame`).
            *   Panic, as the early kernel should not typically cause page faults if mappings are correct.
        *   This handler must be registered in the IDT for the page fault vector (14). This is usually done during `init` (Task 1.2.3 of `init_dev_plan.md`).

---

### Phase 3: Kernel Heap Allocator (`kmalloc` equivalent)

**Goal:** Implement a dynamic memory allocator for the kernel, allowing for allocation and deallocation of arbitrary-sized memory blocks in kernel virtual memory. This is essential for many kernel subsystems that require dynamic data structures.

**Prerequisites:**

*   **Phase 1 (Boot-time PMM):** A physical frame allocator (`BootInfoFrameAllocator` or its successor) must be available to allocate contiguous physical frames for the initial heap area.
*   **Phase 2 (Basic Kernel Paging Setup):** Kernel virtual memory management must be functional, allowing the mapping of physical frames to kernel virtual addresses. The `KERNEL_MAPPER` (e.g., `OffsetPageTable`) must be available.

**Key Modules/Files Involved:**

*   `src/mm/heap/mod.rs` (or `src/mm/kheap.rs`)
*   `src/mm/heap/linked_list_allocator.rs` (if choosing this strategy initially)
*   `src/mm/mod.rs` (to expose `kinit_heap`, `kmalloc`, `kfree`)
*   `src/lib.rs` or `src/allocator.rs` (for `#[global_allocator]` setup)

**Choice of Initial Allocator Strategy:**

*   For simplicity and to get a working heap quickly, a **Fixed-Size Block Allocator** (sometimes called a pool allocator or a simple segregated storage) or a **Linked List Allocator** is often chosen for early kernel development.
    *   **Linked List Allocator:** Manages free memory as a linked list of blocks of varying sizes. Relatively simple to implement, handles fragmentation to some extent by coalescing free blocks.
    *   **Fixed-Size Block Allocator:** Divides the heap area into blocks of predefined sizes. Simpler allocation/deallocation but can suffer from internal fragmentation if object sizes don't match block sizes well. Can be a good precursor or companion to a more general allocator.
*   A full-fledged Slab/Slub allocator is more complex and can be a later enhancement (Phase 3.x or a separate MM phase).
*   **Decision for this plan:** Start with a **Linked List Allocator** due to its reasonable balance of simplicity and flexibility for general-purpose early kernel allocations.

**Sub-Tasks:**

**3.1. Define Heap Data Structures (e.g., for Linked List Allocator):**
    *   **Location:** `src/mm/heap/linked_list_allocator.rs`
    *   **Sub-Task 3.1.1:** Define `HeapBlockHeader` struct:
        *   `size: usize` (size of the block, including the header itself).
        *   `next: Option<&'static mut HeapBlockHeader>` (pointer to the next free block in the list).
        *   `is_free: bool` (or use the `next` pointer being `Some` to indicate free).
        *   (Consider placing the header *before* the allocated data block. Ensure proper alignment.)
    *   **Sub-Task 3.1.2:** Define `LinkedListAllocator` struct:
        *   `head: HeapBlockHeader` (a dummy head node for the free list, simplifying list operations).
        *   `heap_start_addr: VirtAddr`
        *   `heap_end_addr: VirtAddr`
        *   `total_size: usize`
        *   `used_size: usize`

**3.2. Implement `LinkedListAllocator` Logic:**
    *   **Location:** `src/mm/heap/linked_list_allocator.rs`
    *   **Sub-Task 3.2.1:** Implement `LinkedListAllocator::new(heap_start: VirtAddr, heap_size: usize) -> Self`:
        *   Initializes `heap_start_addr`, `heap_end_addr`, `total_size`.
        *   Sets up the `head` dummy node.
        *   Creates an initial large free block covering the entire heap region and adds it to the free list pointed to by `head`.
        *   `unsafe` code will be needed to interpret the raw memory region as `HeapBlockHeader`s.
    *   **Sub-Task 3.2.2:** Implement `LinkedListAllocator::alloc(&mut self, layout: core::alloc::Layout) -> Result<*mut u8, AllocationError>`:
        *   Iterate through the free list (starting from `self.head.next`).
        *   For each free block, check if it's large enough to satisfy `layout.size()` plus any necessary header/alignment overhead for the *allocated* block and a potential *remaining free block*.
        *   **Splitting Blocks:** If a suitable block is found and it's larger than required, split it:
            *   The first part becomes the allocated block (mark as not free).
            *   The remaining part becomes a new free block (update its header, add to free list).
            *   If the block is an exact match or too small to split, use the whole block.
        *   Adjust `used_size`.
        *   Return a pointer to the data area (after the header) of the allocated block.
        *   If no suitable block, return `Err(AllocationError::NoMemory)`.
    *   **Sub-Task 3.2.3:** Implement `LinkedListAllocator::dealloc(&mut self, ptr: *mut u8, layout: core::alloc::Layout)`:
        *   Get the `HeapBlockHeader` for the given `ptr` (by subtracting header size).
        *   Mark the block as free.
        *   Adjust `used_size`.
        *   **Coalescing:** Attempt to merge this newly freed block with its `prev` and `next` blocks in memory if they are also free. This requires iterating the free list or having doubly-linked blocks/boundary tags.
            *   To simplify initially, coalescing might be basic or deferred. A common simple approach is to add the freed block to the front of the free list and only coalesce during allocation if a large enough block isn't found immediately. More robust coalescing involves keeping the free list sorted by address.
    *   **Sub-Task 3.2.4:** Ensure proper alignment handling for allocations as per `layout.align()`. The start of the data region returned by `alloc` must be aligned.

**3.3. Initialize Kernel Heap Area:**
    *   **Location:** `src/mm/heap/mod.rs` or `src/mm/mod.rs`
    *   **Sub-Task 3.3.1:** Define `KERNEL_HEAP_START: VirtAddr` and `KERNEL_HEAP_SIZE: usize` constants.
        *   `KERNEL_HEAP_START` should be in kernel virtual address space, separate from kernel image, direct map, etc.
        *   `KERNEL_HEAP_SIZE` (e.g., 1MB or a few MB initially).
    *   **Sub-Task 3.3.2:** Implement `pub fn kinit_heap(frame_allocator: &mut dyn PhysicalFrameAllocator, mapper: &mut dyn PageTableMapper) -> Result<(), HeapInitializationError>`:
        *   (This function is called once from `init` after PMM and VMM are set up - see `init_dev_plan.md` Task 2.1.3).
        *   `PhysicalFrameAllocator` is the trait for the advanced PMM (e.g. Buddy Allocator if Phase 1 PMM is replaced, or still `BootInfoFrameAllocator` if it's made to implement a richer trait).
        *   `PageTableMapper` is a trait for the `KERNEL_MAPPER` (e.g. `OffsetPageTable`).
        *   Allocate physical frames for the heap:
            *   `num_heap_frames = KERNEL_HEAP_SIZE / PAGE_SIZE`.
            *   Call `frame_allocator.allocate_contiguous_frames(num_heap_frames)` (or allocate one by one if contiguous isn't guaranteed/needed by heap implementation, though contiguous is simpler). Let this be `heap_phys_frames_start_addr`.
        *   Map these physical frames to the virtual address range `[KERNEL_HEAP_START, KERNEL_HEAP_START + KERNEL_HEAP_SIZE)`:
            *   Iterate `num_heap_frames`. For each frame:
                *   `let frame = PhysFrame::containing_address(heap_phys_frames_start_addr + i * PAGE_SIZE);`
                *   `let page = Page::containing_address(KERNEL_HEAP_START + i * PAGE_SIZE);`
                *   `mapper.map_to(page, frame, PageTableFlags::PRESENT | PageTableFlags::WRITABLE | PageTableFlags::NO_EXECUTE, frame_allocator).unwrap().flush();`
        *   Initialize the chosen allocator (e.g., `LinkedListAllocator`) with this virtual memory region:
            *   `let heap_allocator = LinkedListAllocator::new(KERNEL_HEAP_START, KERNEL_HEAP_SIZE);`
            *   Store this `heap_allocator` instance in a global static variable (see 3.4.1).
        *   Define `HeapInitializationError` enum (e.g., `FrameAllocationFailed`, `MappingFailed`).
    *   **Note:** The `frame_allocator` used here for mapping is the one passed to `map_to` for allocating intermediate page table pages, not for the heap frames themselves (those were allocated from the PMM).

**3.4. Setup `#[global_allocator]`:**
    *   **Location:** `src/allocator.rs` (or `src/lib.rs`, or `src/mm/heap/mod.rs`)
    *   **Sub-Task 3.4.1:** Define a wrapper struct for the chosen heap allocator that implements `core::alloc::GlobalAlloc`. This wrapper will typically contain `spin::Mutex<Option<YourHeapAllocatorStruct>>`.
        ```rust
        use core::alloc::{GlobalAlloc, Layout};
        use spin::Mutex;
        // Assuming LinkedListAllocator is the chosen implementation
        use crate::mm::heap::linked_list_allocator::LinkedListAllocator; // Adjust path as needed
        use crate::mm::types::VirtAddr; // Assuming VirtAddr is in types

        pub struct LockedHeap {
            inner: Mutex<Option<LinkedListAllocator>>,
        }

        impl LockedHeap {
            pub const fn empty() -> Self {
                LockedHeap { inner: Mutex::new(None) }
            }

            // Called by kinit_heap
            // Adjusted to take LinkedListAllocator instance directly
            pub unsafe fn init(&self, allocator: LinkedListAllocator) {
                *self.inner.lock() = Some(allocator);
            }
        }

        unsafe impl GlobalAlloc for LockedHeap {
            unsafe fn alloc(&self, layout: Layout) -> *mut u8 {
                if let Some(ref mut allocator) = *self.inner.lock() {
                    // Assuming alloc returns Result<*mut u8, AllocationError>
                    allocator.alloc(layout).unwrap_or(core::ptr::null_mut())
                } else {
                    // Heap not initialized
                    core::ptr::null_mut()
                }
            }

            unsafe fn dealloc(&self, ptr: *mut u8, layout: Layout) {
                if let Some(ref mut allocator) = *self.inner.lock() {
                    allocator.dealloc(ptr, layout);
                } else {
                    // Heap not initialized or attempting to dealloc before init / after destruction
                    // Consider a panic or log here if appropriate for the OS stage
                }
            }
        }
        ```
    *   **Sub-Task 3.4.2:** Declare the static global allocator instance:
        *   `#[global_allocator]`
        *   `static KERNEL_HEAP_ALLOCATOR: LockedHeap = LockedHeap::empty();`
    *   **Sub-Task 3.4.3:** Modify `kinit_heap` (from 3.3.2) to initialize this static instance:
        *   After successfully mapping the heap region and creating the `LinkedListAllocator` instance, call `unsafe { KERNEL_HEAP_ALLOCATOR.init(heap_allocator); }`. (The `init` method in `LockedHeap` should be adjusted to take the `LinkedListAllocator` instance directly).

**3.5. Provide Kernel Allocation/Deallocation Functions (Optional syntactic sugar):**
    *   **Location:** `src/mm/heap/mod.rs` or `src/mm/mod.rs`
    *   **Sub-Task 3.5.1:** (Optional) Define `kmalloc(layout: Layout) -> *mut u8` and `kfree(ptr: *mut u8, layout: Layout)` that internally call `KERNEL_HEAP_ALLOCATOR.alloc/dealloc`. This is mostly for conceptual similarity to C OS development, as Rust code will typically use `Box`, `Vec`, etc., which use the `GlobalAlloc` trait directly.

**3.6. Testing Considerations (Conceptual):**
    *   Early tests would involve allocating small objects and checking pointer validity and non-overlap.
    *   Deallocating and then reallocating to see if memory is reused.
    *   Testing for out-of-memory conditions.
    *   (More advanced) Fragmentation tests, performance tests (much later).

---

### Phase 4: Virtual Address Spaces (VAS) & User Process Memory

**Goal:** Design and implement per-process virtual address spaces, enabling memory isolation between user processes and between user processes and the kernel. This includes managing Virtual Memory Areas (VMAs), handling page faults for user space (demand paging, Copy-on-Write), and supporting `fork()` and `execve()` memory semantics.

**Prerequisites:**

*   **Phase 1 (Boot-time PMM):** Functional physical frame allocator.
*   **Phase 2 (Basic Kernel Paging Setup):** Kernel address space initialized, `KERNEL_MAPPER` active. Direct physical map available.
*   **Phase 3 (Kernel Heap):** Kernel dynamic memory allocation available for VAS metadata (e.g., VMA structures, page table structures if not frame-allocated directly).
*   **`arch` Module:** Functions for page table manipulation (creating/updating entries, TLB flushing), context switching (CR3 loading).
*   **`tasks` Module (Phase 1):** Basic kernel thread infrastructure (TCB/`Task` struct, scheduler).
*   **`interrupts` Module (Phase 1):** Page fault exception handler registered, capable of dispatching to MM-specific handlers.

**Key Modules/Files Involved:**

*   `src/mm/vas/mod.rs` (new, for AddressSpace)
*   `src/mm/vas/vma.rs` (new, for VirtualMemoryArea)
*   `src/mm/paging/mapper.rs` (or similar, needs user-space mapping capabilities)
*   `src/mm/paging/page_fault.rs` (new or expanded user-space page fault handler logic)
*   `src/mm/mod.rs`
*   `src/tasks/process.rs` (or `mod.rs`, for integrating `Arc<AddressSpace>` into `Task` struct)
*   `src/fs/elf.rs` (new, or `fs/exec.rs`, for ELF loading)

**4.1. Define Core VAS Data Structures:**
    *   **Location:** `src/mm/vas/vma.rs`
    *   **Sub-Task 4.1.1: Define `VmaPermissions` Bitflags:**
        *   `READ`, `WRITE`, `EXECUTE`, `USER_ACCESSIBLE`.
    *   **Sub-Task 4.1.2: Define `VmaFlags` Bitflags:**
        *   `SHARED`: Changes propagate to other sharers (e.g., file mappings, `mmap` with `MAP_SHARED`).
        *   `PRIVATE`: Changes are private (Copy-on-Write for writable mappings).
        *   `ANONYMOUS`: Backed by anonymous memory (zero-filled on demand, swap-backed later).
        *   `FILE_BACKED`: Backed by a file.
        *   `GROW_DOWN`: For stack segments.
        *   `GROW_UP`: For heap segments (less common for explicit VMAs, `brk` usually manages this).
        *   `LOCKED`: (Future) Memory is locked (pinned).
        *   `EXECUTABLE_IMAGE`: VMA contains executable code from the binary file.
    *   **Sub-Task 4.1.3: Define `VmaBacking` Enum (or similar):**
        *   `Anonymous`
        *   `File { file: Arc<fs::FileInode>, offset: u64, file_permissions: VmaPermissions }` (Store original file permissions for CoW checks).
        *   (Future: `Swap { swap_slot: SwapSlot }`)
    *   **Sub-Task 4.1.4: Define `VirtualMemoryArea` (VMA) Struct:**
        *   `start_addr: VirtAddr`
        *   `end_addr: VirtAddr` (or `size: usize`)
        *   `permissions: VmaPermissions`
        *   `flags: VmaFlags`
        *   `backing: VmaBacking`
        *   `owner_as: Weak<AddressSpace>` (Weak reference to parent `AddressSpace` to avoid cycles, if needed).
        *   Methods: `contains_addr(&self, addr: VirtAddr) -> bool`, `len(&self) -> usize`.

    *   **Location:** `src/mm/vas/mod.rs`
    *   **Sub-Task 4.1.5: Define `AddressSpace` Struct:**
        *   `id: AsId` (Address Space ID, if needed for global tracking or ASID in HW).
        *   `page_table_root: PhysFrame` (Physical frame holding the top-level page table, e.g., PML4). This frame is owned by this `AddressSpace`.
        *   `vmas: LinkedList<Arc<RwLock<VirtualMemoryArea>>>` (or `BTreeMap<VirtAddr, Arc<RwLock<VirtualMemoryArea>>>` keyed by start address, or eventually a more performant tree like Red-Black or Maple Tree). `RwLock` allows concurrent reads but exclusive writes to individual VMAs.
        *   `lock: RwLock<()>` (A lock to protect the `vmas` list/tree itself during modifications like adding/removing VMAs or major changes like fork/exec).
        *   `stats: AddressSpaceStats` (e.g., `total_vm_pages`, `rss_pages`).
        *   (Future: `exe_file: Option<Arc<fs::FileInode>>`).

**4.2. Implement `AddressSpace` Core Functionality:**
    *   **Location:** `src/mm/vas/mod.rs`
    *   **Sub-Task 4.2.1: `AddressSpace::create(kernel_mapper: &PageTableMapper) -> Result<Arc<Self>, VasError>`:**
        *   Allocate a new physical frame for the top-level page table (PML4) using the PMM. Zero it.
        *   **Kernel Space Mapping:** Copy kernel-space mappings from the `kernel_mapper`'s PML4 into the new PML4. This ensures kernel code, data, and the direct physical map are accessible from the user process's context when in kernel mode (e.g., during syscalls or interrupts).
        *   Initialize `vmas` list/tree as empty.
        *   Initialize `lock` and `stats`.
        *   Return `Arc` of the new `AddressSpace`.
        *   `VasError` enum: `FrameAllocationFailed`, `PageTableSetupFailed`.
    *   **Sub-Task 4.2.2: `AddressSpace::destroy(&mut self, pmm: &mut impl PhysicalFrameAllocator, kernel_mapper: &PageTableMapper)` (or `impl Drop`):**
        *   Acquire exclusive lock on `self.lock`.
        *   Iterate through all `vmas`:
            *   For each VMA, unmap its entire virtual range from the page tables associated with `self.page_table_root`. This involves walking the page tables, freeing physical frames backing anonymous memory (if not swapped), decrementing reference counts for shared frames, and freeing intermediate page table pages (PTs, PDs, PDPTs) belonging to this user address space.
        *   Free the `self.page_table_root` frame itself using the PMM.
        *   (Needs careful handling of shared frames and CoW).
    *   **Sub-Task 4.2.3: `AddressSpace::find_vma(&self, addr: VirtAddr) -> Option<Arc<RwLock<VirtualMemoryArea>>>`:**
        *   Acquire read lock on `self.lock`.
        *   Search `self.vmas` for a VMA that contains `addr`.
    *   **Sub-Task 4.2.4: `AddressSpace::find_vma_intersection(&self, start_addr: VirtAddr, end_addr: VirtAddr) -> Option<Arc<RwLock<VirtualMemoryArea>>>`:**
        *   Acquire read lock on `self.lock`.
        *   Search `self.vmas` for any VMA that overlaps with the given range.

**4.3. Implement VMA Mapping and Unmapping within an `AddressSpace`:**
    *   **Location:** `src/mm/vas/mod.rs` (methods on `AddressSpace`)
    *   **Sub-Task 4.3.1: `AddressSpace::map_vma(&self, desired_vma: VirtualMemoryArea, pmm: &mut impl PhysicalFrameAllocator, user_space_mapper: &mut PageTableMapper) -> Result<VirtAddr, VasError>`:**
        *   (Simplified `mmap` core logic). `user_space_mapper` is a `PageTableMapper` configured for `self.page_table_root`.
        *   Acquire exclusive lock on `self.lock`.
        *   **Find Free Range:** If `desired_vma.start_addr` is zero or a hint, find an appropriate unallocated range in the user virtual address space that can fit `desired_vma.size`. This involves iterating existing VMAs. (Implement `get_unmapped_area` logic).
        *   **Check for Overlaps:** Ensure the chosen range doesn't overlap with existing VMAs unless allowed (e.g., `MAP_FIXED` might replace).
        *   Create `Arc<RwLock<VirtualMemoryArea>>` from `desired_vma`.
        *   Insert the new VMA into `self.vmas`, maintaining order.
        *   **Page Table Entries (Demand Paging):** For anonymous, non-locked memory, typically no page table entries are created at this point. Pages are allocated and mapped on first access (page fault). For file-backed or pre-populated memory, initial PTEs might be set up.
        *   Update `self.stats`.
        *   Return `Ok(actual_start_address)`.
    *   **Sub-Task 4.3.2: `AddressSpace::unmap_range(&self, start_addr: VirtAddr, size: usize, pmm: &mut impl PhysicalFrameAllocator, user_space_mapper: &mut PageTableMapper) -> Result<(), VasError>`:**
        *   (Simplified `munmap` core logic).
        *   Acquire exclusive lock on `self.lock`.
        *   Find all VMAs that overlap with the range `[start_addr, start_addr + size)`.
        *   For each affected VMA:
            *   **Unmap Pages:** Walk page tables for the overlapping part of the VMA. For each present PTE:
                *   Get the physical frame.
                *   If anonymous & owned by this VMA, deallocate the frame using PMM.
                *   If shared (file-backed or CoW parent), decrement frame ref count.
                *   Clear the PTE.
            *   **Adjust VMA:**
                *   If VMA is fully contained in unmap range, remove it from `self.vmas`.
                *   If VMA is partially overlapped, shrink it or split it into two VMAs.
        *   Flush TLB for the unmapped range (using `user_space_mapper` or `arch` functions).
        *   Update `self.stats`.

**4.4. User-Space Page Fault Handling:**
    *   **Location:** `src/mm/paging/page_fault.rs`
    *   **Sub-Task 4.4.1: Extend main Page Fault Handler (from Phase 2.5):**
        *   The IDT-registered page fault handler needs to:
            *   Determine if the fault occurred in user mode or kernel mode (e.g., from CS register, error code).
            *   If kernel mode fault, use the kernel page fault handler (Phase 2.5.1).
            *   If user mode fault:
                *   Get current task's `Arc<AddressSpace>`.
                *   Call `address_space.handle_user_page_fault(fault_addr, error_code, pmm, user_space_mapper)`.
    *   **Sub-Task 4.4.2: `AddressSpace::handle_user_page_fault(&self, fault_addr: VirtAddr, error_code: PageFaultErrorCode, pmm: &mut impl PhysicalFrameAllocator, user_space_mapper: &mut PageTableMapper) -> Result<(), PageFaultError>`:**
        *   Acquire read lock on `self.lock` (might need to be upgradable or released/re-acquired if modifications are needed).
        *   Find VMA containing `fault_addr` using `self.find_vma()`. If no VMA, return `Err(SegmentationFault)`.
        *   Let `vma = vma.read()` (or `write()` if CoW might happen).
        *   **Permission Check:**
            *   If `error_code` indicates a write fault but `!vma.permissions.contains(VmaPermissions::WRITE)`, return `Err(SegmentationFault)`.
            *   If `error_code` indicates an instruction fetch fault but `!vma.permissions.contains(VmaPermissions::EXECUTE)`, return `Err(SegmentationFault)`.
            *   If `error_code` indicates a protection violation on a present page (e.g., write to read-only page):
                *   **Copy-on-Write (CoW):** If `vma.flags.contains(VmaFlags::PRIVATE)` and `vma.permissions.contains(VmaPermissions::WRITE)`:
                    1.  Get current PTE and mapped `PhysFrame`.
                    2.  If frame ref count > 1 (or special CoW marker):
                        *   Allocate a new `PhysFrame` (`new_frame`) from PMM.
                        *   Copy content from `old_frame` to `new_frame` (via kernel direct map).
                        *   Update current task's PTE to map to `new_frame` with `WRITABLE` permission.
                        *   Decrement `old_frame`'s ref count.
                        *   Flush TLB for `fault_addr`.
                        *   Return `Ok(())`.
                    3.  Else (frame ref count == 1): This page is exclusively owned. Simply mark PTE as writable, flush TLB. Return `Ok(())`.
                *   Else (not a CoW situation for a protection fault), return `Err(SegmentationFault)`.
        *   **Page Not Present Fault (`error_code.contains(PageFaultErrorCode::PAGE_NOT_PRESENT)`):**
            *   **Demand Paging (Anonymous VMA):** If `vma.backing == VmaBacking::Anonymous`:
                1.  Allocate a new physical frame from PMM. Zero it.
                2.  Map `fault_addr` (page-aligned) to this new frame in the page tables with `vma.permissions`.
                3.  Flush TLB. Return `Ok(())`.
            *   **Demand Paging (File-Backed VMA):** If `vma.backing == VmaBacking::File { file, offset, .. }`:
                1.  Calculate file offset for the faulting page: `file_offset = offset + (fault_addr - vma.start_addr)`.
                2.  Allocate a new physical frame from PMM.
                3.  Read page content from `file` at `file_offset` into the frame (via kernel buffer cache or direct read).
                4.  Map `fault_addr` to this frame with `vma.permissions`.
                5.  Flush TLB. Return `Ok(())`.
            *   (Future: Swap-in logic).
        *   Define `PageFaultError` enum: `SegmentationFault`, `OutOfMemory`.

**4.5. Integration with `fork()` System Call:**
    *   **Location:** `src/mm/vas/mod.rs` (for `AddressSpace` method), `src/tasks/process.rs` (for `fork` logic).
    *   **Sub-Task 4.5.1: `AddressSpace::duplicate_for_fork(&self, new_pml4_frame: PhysFrame, pmm: &mut impl PhysicalFrameAllocator, kernel_mapper: &PageTableMapper, new_as_mapper: &mut PageTableMapper) -> Result<Arc<Self>, VasError>`:**
        *   (`new_pml4_frame` is pre-allocated by the caller for the child's AS).
        *   Create new `AddressSpace` instance (`child_as`) using `new_pml4_frame`.
        *   Acquire read lock on `self.lock` (parent AS) and exclusive lock on `child_as.lock`.
        *   Copy kernel mappings from `kernel_mapper` to `child_as`'s page tables (as in `AddressSpace::create`).
        *   Iterate through `self.vmas` (parent VMAs):
            *   For each `parent_vma`:
                *   If `parent_vma.flags.contains(VM_DONTCOPY)`, skip.
                *   Create `child_vma = parent_vma.deep_copy()` (adjusting owner, etc.).
                *   Add `child_vma` to `child_as.vmas`.
                *   **Page Table Duplication (`copy_page_range` logic):**
                    *   Iterate pages in `parent_vma`'s virtual range.
                    *   For each page, get parent's PTE using `kernel_mapper` (or a temporary mapper for parent's AS).
                    *   If parent PTE is present:
                        *   Let `phys_frame` be the frame from parent PTE.
                        *   If `parent_vma.flags.contains(VmaFlags::SHARED)`:
                            *   Map page in `child_as` (using `new_as_mapper`) to `phys_frame` with same permissions.
                            *   Increment `phys_frame` ref count (requires PMM to support ref counting).
                        *   Else (`VmaFlags::PRIVATE`):
                            *   If `parent_vma.permissions.contains(VmaPermissions::WRITE)` (CoW candidate):
                                *   Mark parent's PTE as read-only (using `kernel_mapper`). Flush TLB for parent.
                                *   Map page in `child_as` to `phys_frame` as read-only (using `new_as_mapper`).
                                *   Increment `phys_frame` ref count.
                            *   Else (read-only private):
                                *   Map page in `child_as` to `phys_frame` as read-only.
                                *   Increment `phys_frame` ref count.
                    *   (Intermediate page table pages for child AS must be allocated from `pmm` as needed by `new_as_mapper.map_to`).
        *   Copy relevant `AddressSpaceStats`.
        *   Return `Ok(child_as)`.
    *   **Note on Frame Reference Counting:** Phase 1 PMM needs to be enhanced to support reference counting for physical frames for CoW and shared mappings to work correctly.

**4.6. Integration with `execve()` System Call:**
    *   **Location:** `src/mm/vas/mod.rs`, `src/fs/elf.rs` (or `fs/exec.rs`), `src/tasks/process.rs`.
    *   **Sub-Task 4.6.1: `AddressSpace::reset_for_exec(&mut self, pmm: &mut impl PhysicalFrameAllocator, kernel_mapper: &PageTableMapper)`:**
        *   Acquire exclusive lock on `self.lock`.
        *   Call `self.unmap_range()` for the entire user-space portion of the address space to clear all existing user VMAs and page table entries.
        *   (The top-level page table frame `self.page_table_root` is kept, but its user-space entries are cleared. Kernel mappings remain).
        *   Reset `self.vmas` to empty. Reset `self.stats`.
    *   **Sub-Task 4.6.2: ELF Loading Logic (e.g., in `src/fs/elf.rs`):**
        *   `fn load_elf_into_address_space(as: &Arc<AddressSpace>, elf_file: Arc<fs::FileInode>, pmm: &mut ..., user_space_mapper: &mut ...) -> Result<VirtAddr /*entry_point*/, ElfLoadError>`
        *   Parse ELF headers from `elf_file`.
        *   For each loadable segment (PT_LOAD):
            *   Create a `VirtualMemoryArea` struct:
                *   `start_addr`, `size` from segment header.
                *   `permissions` from segment flags (PF_R, PF_W, PF_X).
                *   `flags`: `ANONYMOUS` if BSS, `FILE_BACKED` and `EXECUTABLE_IMAGE` (for text) otherwise.
                *   `backing`: `File { file: elf_file.clone(), offset: segment_file_offset, ... }` or `Anonymous`.
            *   Call `as.map_vma(vma, ...)` to add it.
            *   (For non-BSS segments, pages could be demand-paged from the file, or pre-loaded if desired).
        *   Create VMA for the stack.
        *   Return program entry point from ELF header.
    *   **Sub-Task 4.6.3: `tasks` module `execve` implementation:**
        *   Gets current task's `AddressSpace` (or creates new one if `CLONE_VM` wasn't used and this is a new `mm_struct`).
        *   Calls `address_space.reset_for_exec(...)`.
        *   Calls `load_elf_into_address_space(...)`.
        *   Sets up new stack with args/envp.
        *   Updates task's instruction pointer to the new entry point.
        *   Switches CR3 if `AddressSpace` was newly created for `execve` (less common; usually `execve` reuses the existing `mm_struct` after clearing it).

**4.7. Associate `AddressSpace` with `Task`:**
    *   **Location:** `src/tasks/process.rs` (or `mod.rs`)
    *   **Sub-Task 4.7.1:** Modify `Task` struct to include `pub address_space: Option<Arc<AddressSpace>>`. It's `Option` because kernel threads might not have a user VAS initially, or might share `init_mm`.
    *   **Sub-Task 4.7.2:** When a new user process is created (first task in a new address space, or after `fork` without `CLONE_VM`), its `Task::address_space` field is populated with the `Arc<AddressSpace>`.
    *   **Sub-Task 4.7.3:** Context switch (`arch` module) for user processes must load `task.address_space.as_ref().unwrap().page_table_root.start_address()` into CR3.


---
## Integration Points

*   **Architecture Specifics (`src/arch`):** For MMU control, page table formats, TLB management, CR3 loading, CR0/CR4 updates. Linker symbols for kernel image layout.
*   **Kernel Core (`src/lib.rs` or `kmain`):** Calls `mm::init_boot_info_allocator()`, then `mm::kernel_page_table_init()`, then `arch::enable_paging()`.
*   **Initialization (`src/init`):** Orchestrates these calls in the correct sequence during boot. The `init_dev_plan.md` Phase 1.1 (Bootstrapping & Early Arch-Specific Setup) and 1.2.4 (Initial Physical Memory Management) directly correspond to these `mm` phases.
*   **Panic Handler:** Uses memory info for diagnostics.
*   **Process Management (Future):** For creating and managing address spaces for processes.
