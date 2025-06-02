# Interrupts & Advanced Exception Handling (`interrupts`) Development Plan

## Overview

This document outlines the development plan for the Interrupts and Advanced Exception Handling module (`interrupts`) for GritOS. This module is responsible for managing hardware interrupts (IRQs) beyond the initial setup done by `arch` and `init`, providing a dynamic registration and dispatch mechanism for device drivers. It also aims to enhance the handling and reporting for CPU exceptions not directly managed by other subsystems like the Memory Management (MM) module's page fault handler. Robust and efficient interrupt and exception handling is critical for system stability, responsiveness, and debugging.

## Responsibilities

The `interrupts` module will be responsible for:

*   **Dynamic IRQ Management:**
    *   Providing APIs for device drivers to request (claim) and release (free) hardware IRQ lines.
    *   Managing shared IRQs, allowing multiple drivers to handle interrupts on the same line if the hardware supports it.
    *   Enabling, disabling, and masking/unmasking specific IRQ lines at the interrupt controller level (via `arch` abstractions).
*   **Interrupt Dispatching:**
    *   Receiving interrupt notifications from the low-level assembly stubs (set up by `arch` and `init`).
    *   Identifying the source IRQ and dispatching control to the appropriate registered Rust handler(s).
    *   Acknowledging interrupts (EOI - End Of Interrupt) with the interrupt controller(s).
*   **IRQ Descriptor Management:**
    *   Maintaining data structures (`IrqDescriptor`) that store information for each IRQ line, including its status (free, allocated), registered handler(s), flags (e.g., shared, edge/level triggered), and potentially statistics.
*   **Deferred Work Foundations (Tasklets/Work Queues - Initial Concepts):**
    *   Laying the conceptual groundwork or providing very basic mechanisms for deferring parts of interrupt handling to a non-interrupt context (e.g., a kernel thread or a simple work queue). Full implementation might be a later phase or a separate module.
*   **Advanced CPU Exception Analysis and Reporting:**
    *   For CPU exceptions not handled by specific subsystems (e.g., General Protection Fault, Double Fault, SIMD exceptions, etc.), provide more detailed analysis and reporting than basic panic messages. This includes comprehensive register dumps, stack traces, and potentially disassembly of faulting instructions.
*   **(Future/SMP) IRQ Affinity and Balancing:**
    *   Distributing hardware interrupts across multiple CPUs for better performance and responsiveness.
    *   Allowing drivers or system policy to set CPU affinity for specific IRQs.

## Inspiration from Linux (kernel/irq/, arch/../kernel/irq.c, arch/../kernel/traps.c)

The design will draw inspiration from Linux's interrupt and exception handling mechanisms:

*   **IRQ Management:** `kernel/irq/`, `include/linux/interrupt.h` (for `request_irq`, `free_irq`, `irq_desc` structures, handler chains, threaded IRQs).
*   **Interrupt Controllers:** Driver-specific code for PIC, IO-APIC, MSI/MSI-X in `drivers/irqchip/` and `arch/.../kernel/irqmanage.c`.
*   **Exception Handling:** `arch/.../kernel/traps.c`, `arch/.../kernel/cpu/mce/`, `arch/.../mm/fault.c` for specific fault types and general exception entry/exit paths.
*   **Softirqs and Tasklets:** `kernel/softirq.c`, `include/linux/interrupt.h` for deferred work.

## Rust Implementation Strategy

*   **Safety and Abstraction:**
    *   Use Rust's safety features to create safe APIs for IRQ management, minimizing `unsafe` code to an absolute minimum (primarily in arch-specific dispatcher stubs and controller interaction).
    *   Define traits for interrupt handlers (`IrqHandler`) and potentially for interrupt controller abstractions if needed beyond what `arch` provides directly.
*   **Data Structures:**
    *   `IrqDescriptor` structs will be central, likely managed in a global array or `HashMap` protected by locks.
    *   Use `Arc<Mutex<...>>` or `SpinLock` for shared mutable data, ensuring concurrency safety.
*   **Modularity:**
    *   Core logic in `src/interrupts/mod.rs`.
    *   Architecture-specific dispatch entry points and low-level controller interactions remain in `src/arch/[arch]/interrupts.rs` or `src/arch/[arch]/kernel/irq.rs`.
*   **Error Handling:** Use `Result` for operations like `request_irq` (e.g., `IrqError::InUse`, `IrqError::InvalidIrqNum`).
*   **Deferred Work:** Initially, deferred work might involve simply waking a registered kernel thread. More complex tasklet/work queue systems are future enhancements.

## Key Components

### A. IRQ Descriptor and Management
*   **Purpose:** To represent and manage the state of each hardware interrupt line.
*   **Details:** `struct IrqDescriptor { status: IrqStatus (Free, Active, SharedActive), line_type: IrqLineType (Edge, Level), device_name: Option<&'static str>, primary_handler: Option<IrqHandlerFn>, shared_handlers: Vec<IrqHandlerFn>, statistics: IrqStats (count, spurious_count), arch_specific_data: Box<dyn Any> }`. A global array `IRQ_DESCRIPTORS: [Mutex<Option<IrqDescriptor>>; MAX_IRQS]` or similar.

### B. Interrupt Dispatching
*   **Purpose:** Core logic to route an incoming hardware interrupt to the correct registered handler(s).
*   **Details:**
    *   Low-level assembly stubs (per IRQ vector) call a common Rust entry point `generic_irq_handler(irq_num, trap_frame)`.
    *   `generic_irq_handler` looks up the `IrqDescriptor`, calls handler(s), acknowledges IRQ via `arch` functions.

### C. Handler Registration and Lifecycle
*   **Purpose:** APIs for drivers to request, configure, and release IRQs.
*   **Details:**
    *   `request_irq(irq: u32, handler: IrqHandlerFn, flags: IrqFlags, name: &'static str) -> Result<()>`
    *   `free_irq(irq: u32, handler_id: Option<*const ()>)` (handler_id for shared IRQs).
    *   `enable_irq(irq: u32)`, `disable_irq(irq: u32)` (mask/unmask at controller).

### D. Deferred Work Mechanisms (Foundations)
*   **Purpose:** Allow ISRs to schedule less critical parts of interrupt processing to run outside of true interrupt context.
*   **Details (Initial):** Define a trait `DeferredWork` and a simple mechanism to queue work items that are processed by dedicated kernel threads (from `tasks` module) or a periodic `initcall`-like mechanism.

### E. Advanced CPU Exception Analysis & Reporting
*   **Purpose:** Provide detailed diagnostic information for unhandled CPU exceptions (beyond page faults).
*   **Details:** For exceptions like General Protection Fault (#GP), Double Fault (#DF), SIMD Exception (#XM), etc.:
    *   Detailed register dump (GPRs, segment registers, control registers like CR0-CR4).
    *   Stack trace (if possible and reliable from fault context).
    *   Attempt to disassemble faulting instruction.
    *   Clearer panic messages indicating the type of fault and likely cause.

### F. (Future/SMP) IRQ Affinity and Balancing
*   **Purpose:** Manage interrupt distribution in a multi-processor environment.
*   **Details:** Placeholders for future design of IRQ affinity settings, IRQ migration, and load balancing algorithms.

## Phased Development

---
### Phase 1: Dynamic IRQ Management & Advanced Exception Reporting
This phase focuses on establishing a robust system for drivers to dynamically request and handle interrupts, and improving diagnostics for critical CPU exceptions. **It also lays the groundwork for early boot interrupt configuration, including CPU exception handlers and basic hardware IRQ setup (e.g., for the system timer), as required by the `init` module.**

*   **1.1. IRQ Descriptor and Global Registry:**
    *   **Data Structures:**
        *   `struct IrqAction { handler: fn(&TrapFrame, u32) -> IrqResult, flags: IrqFlags, dev_name: &'static str, dev_id: *mut () /* For shared IRQs */ }`.
        *   `struct IrqDescriptor { lock: SpinLock, status: IrqStatus (Free, InUse, Shared), actions: LinkedList<Arc<IrqAction>>, controller_ops: Arc<dyn IrqControllerOps>, depth: u32 /* For disabling/enabling */ }`.
        *   `static IRQ_DESCRIPTORS: [OnceCell<IrqDescriptor>; MAX_IRQS]`. (Or `Mutex<Vec<Option<IrqDescriptor>>>` if MAX_IRQS is large or dynamic).
        *   `IrqFlags` enum/bitflags: `SHARED`, `EDGE_TRIGGERED`, `LEVEL_TRIGGERED`, `PER_CPU`.
        *   `IrqResult` enum: `Handled`, `NotHandled`.
        *   `IrqStatus` enum: `Free`, `InUse`, `Shared`.
        *   `trait IrqControllerOps { fn startup(&self, irq_num: u32) -> Result<(), IrqError>; fn shutdown(&self, irq_num: u32) -> Result<(), IrqError>; fn enable(&self, irq_num: u32); fn disable(&self, irq_num: u32); fn ack(&self, irq_num: u32); fn eoi(&self, irq_num: u32); }` (Implemented by `arch` specific controller drivers like PIC, APIC).
    *   **Initialization:** `interrupts::init_irq_system(controllers: Vec<Arc<dyn IrqControllerOps>>)` called by `init` after basic arch interrupt setup (IDT is mostly ready, PIC/APIC drivers might provide their ops here). Initializes `IRQ_DESCRIPTORS`. **Note: During early kernel boot (as orchestrated by the `init` module, see `init_dev_plan.md` Task 1.2.3), `init_irq_system` will be called with `IrqControllerOps` instances provided by the `arch` module for the primary interrupt controller (e.g., PIC, then later I/O APIC). This sets up the foundational interrupt handling structures.**
*   **1.2. IRQ Registration and Unregistration API:**
    *   `pub fn request_irq(irq_num: u32, action: IrqAction) -> Result<(), IrqRequestError>`:
        1.  Validate `irq_num`. Get `IrqDescriptor` (or its `OnceCell` initializer).
        2.  Lock descriptor.
        3.  If `action.flags.contains(IrqFlags::SHARED)`:
            *   Ensure existing actions (if any) also have `SHARED` flag. If not, `Err(MismatchShared)`.
            *   Add new `IrqAction` to `actions` list. Status becomes `Shared`.
        4.  Else (exclusive): If `status` is `Free`, set to `InUse`, add `IrqAction`. Else return `Err(InUse)`.
        5.  If this is the first action added to this IRQ line (i.e., it was `Free`), call `descriptor.controller_ops.startup(irq_num)` then `descriptor.controller_ops.enable(irq_num)`.
    *   `pub fn free_irq(irq_num: u32, dev_id: *mut ()) -> Result<(), IrqFreeError>`:
        1.  Validate. Get `IrqDescriptor`. Lock.
        2.  Remove `IrqAction` from `actions` list where `action.dev_id == dev_id`. If not found, `Err(NotFound)`.
        3.  If `actions` list becomes empty: Set `status` to `Free`, call `descriptor.controller_ops.disable(irq_num)` then `descriptor.controller_ops.shutdown(irq_num)`.
    *   `IrqRequestError`, `IrqFreeError` enums.
    *   **Note: For early boot (see `init_dev_plan.md` Task 1.2.6), `request_irq` will be critically used by the system timer driver (e.g., PIT) to register its handler, which is essential for enabling scheduler ticks.**
*   **1.3. Interrupt Dispatcher Logic:**
    *   **Arch-Specific Assembly Stubs:** (Already set up by `init` Phase 1.2.3, but now call a common Rust entry). For each vector (0-255 on x86), an assembly stub that:
        1.  Pushes registers (constructs `TrapFrame`).
        2.  Calls `rust_irq_handler_entry(vector_num: u32, frame_ptr: *mut TrapFrame)`.
        3.  Pops registers, `iretq`.
    *   **Common Rust Entry (`rust_irq_handler_entry`):**
        *   Convert `vector_num` to `kernel_irq_num` (e.g., `vector_num - HW_IRQ_BASE_OFFSET`).
        *   Call `generic_irq_dispatcher(kernel_irq_num, &mut *frame_ptr)`.
    *   **`generic_irq_dispatcher(irq_num: u32, trap_frame: &mut TrapFrame)`:**
        1.  Get `IrqDescriptor` for `irq_num`. If None or not initialized, panic or log spurious IRQ.
        2.  Lock descriptor.
        3.  Call `descriptor.controller_ops.ack(irq_num)` (especially for level-triggered before handler).
        4.  `was_handled = IrqResult::NotHandled`.
        5.  For each `action` in `descriptor.actions`:
            *   `result = action.handler(trap_frame, irq_num)`.
            *   If `result == IrqResult::Handled`, `was_handled = IrqResult::Handled`. (If not shared, could break here).
        6.  If `was_handled == IrqResult::NotHandled`, increment spurious count for this IRQ (potentially calling more specific controller ops to check for PIC spurious IRQs as detailed in `init_dev_plan.md` Task 1.2.3).
        7.  Call `descriptor.controller_ops.eoi(irq_num)`.
        8.  Unlock descriptor.
    *   **Note on CPU Exceptions: While hardware IRQs (like the timer) might use a `generic_irq_dispatcher` via `rust_irq_handler_entry`, CPU exceptions (vectors 0-31 on x86) will typically have their assembly stubs (set up by `arch` module) point to more specialized Rust entry points that directly call the relevant handlers detailed in Section 1.5. These specific entry points are also configured in the IDT by the `arch` module during early boot.**
*   **1.4. IRQ Line Enable/Disable Control (Refined):**
    *   `pub fn enable_irq_line(irq_num: u32)`: Gets descriptor, locks, if `depth == 0` calls `controller_ops.enable()`.
    *   `pub fn disable_irq_line(irq_num: u32)`: Gets descriptor, locks, calls `controller_ops.disable()`. This is a forceful disable at controller level.
    *   `pub fn disable_irq_nosync(irq_num: u32)`: Like `disable_irq` but doesn't wait for current handlers to finish (more dangerous, for specific scenarios like shutdown).
    *   The `depth` counter in `IrqDescriptor` is for nested `disable_irq`/`enable_irq` calls that don't directly touch hardware mask until top level. The functions above are for direct control.
*   **1.5. Advanced CPU Exception Reporting (Refined):**
    *   **As part of the early trap initialization sequence (see `init_dev_plan.md` Task 1.2.3), handlers for these critical CPU exceptions must be registered in the IDT by the `arch` module, and these handlers are implemented by the `interrupts` module (specifically, `src/interrupts/exceptions.rs`).**
    *   **Target Exceptions:** General Protection Fault (#GP), Double Fault (#DF), Stack Segment Fault (#SS), SIMD Exceptions (#XM, #XF), Machine Check (#MC), NMI, Debug, Breakpoint.
    *   **Dedicated Handlers (in `src/interrupts/exceptions.rs`, called by `arch` stubs):**
        *   `fn handle_general_protection_fault(frame: &TrapFrame, error_code: u64)` etc.
        *   Each handler:
            1.  Prints clear fault name, error code, faulting virtual address (if applicable, e.g. from CR2 for #PF, though #PF is mainly MM's).
            2.  Prints full register dump from `frame`.
            3.  Attempts kernel stack trace: unwind based on frame pointers (RBP on x86_64) if available and stack is valid.
            4.  (Ambitious) If fault in kernel mode, attempt to read instruction bytes around `frame.instruction_pointer` and print them (requires mapped kernel code).
            5.  Panic with all collected information.
    *   **Double Fault Handler:** Must ensure it runs on IST stack. Its actions are minimal: print basic info to emergency console (e.g., serial, if VGA might be unsafe) and halt/reboot. Cannot rely on much kernel infrastructure.
    *   **NMI Handler:** Similar robustness to Double Fault. Log NMI source if possible (arch-specific).
---
### Phase 2: Deferred Work Mechanisms & SMP Foundations

**Goal:** To provide mechanisms for deferring interrupt handler work to a non-interrupt context (work queues) and to lay the foundational elements for Symmetric Multi-Processing (SMP) interrupt handling, primarily by enabling architecture-level support for IRQ routing and affinity.

**Prerequisites:**

*   Phase 1 of `interrupts` module (dynamic IRQ management, `IrqControllerOps`).
*   `tasks` module: Kernel thread creation (`create_kernel_thread`), scheduler, and synchronization primitives (Mutexes, Semaphores, as per `tasks_dev_plan.md` Phase 1 & 3.2).
*   Kernel Heap (`mm` module Phase 3) for dynamic allocations (e.g., `WorkItem`s if boxed, `LinkedList` nodes).

**Key Modules/Files Involved:**

*   `src/interrupts/workqueue.rs` (new or expanded)
*   `src/interrupts/mod.rs`
*   `src/arch/[target_arch]/irq.rs` (or `ioapic.rs`, `msi.rs` for affinity)
*   `src/arch/[target_arch]/smp.rs` (if SMP-specific data structures are needed by `arch`)
*   `src/kernel/sync.rs` (or wherever `Semaphore` is defined)

**2.1. Simple Work Queue Implementation:**
    *   **Goal:** Allow ISRs to queue a function call with an argument to be executed by a dedicated kernel thread.
    *   **Location:** `src/interrupts/workqueue.rs` (and potentially `mod.rs` for registration).
    *   **Sub-Task 2.1.1: Define `WorkItem` Structure:**
        *   `struct WorkItem { function: fn(data: usize), data: usize }`
            *   Consider using `Box<dyn FnOnce() + Send>` for more flexibility if heap is reliable, to allow capturing environments. For initial simplicity, `fn(data: usize)` is acceptable. `data: usize` can be a raw pointer cast.
    *   **Sub-Task 2.1.2: Define `WorkQueue` Structure:**
        *   `name: &'static str`
        *   `task_list: spin::Mutex<LinkedList<Box<WorkItem>>>` (Using `Box<WorkItem>` assumes heap is available for work items themselves).
        *   `worker_thread_pid: Option<tasks::PID>` (Store PID of the worker).
        *   `work_pending_sem: Arc<kernel::sync::Semaphore>` (Semaphore from `tasks` or a dedicated sync module).
    *   **Sub-Task 2.1.3: Implement `WorkQueue::new(name: &'static str, worker_priority: tasks::Priority) -> Arc<WorkQueue>`:**
        *   Create `Arc<WorkQueue>` with an empty list and new semaphore.
        *   Spawn a new kernel thread using `tasks::create_kernel_thread()`:
            *   The thread entry function will be `work_queue_runner_fn`.
            *   Pass a clone of the `Arc<WorkQueue>` as an argument to `work_queue_runner_fn`.
            *   Store the returned PID in `worker_thread_pid`.
        *   Return the `Arc<WorkQueue>`.
    *   **Sub-Task 2.1.4: Implement `work_queue_runner_fn(wq_arc_ptr: *mut WorkQueue)` (the worker thread's main loop):**
        *   Convert `wq_arc_ptr` back to `Arc<WorkQueue>`.
        *   Loop:
            *   `wq.work_pending_sem.acquire()` (blocks until work is available).
            *   Lock `wq.task_list`.
            *   Pop a `Box<WorkItem>` from the front of the list.
            *   Unlock `wq.task_list`.
            *   If a `work_item` was popped:
                *   `(work_item.function)(work_item.data);`
                *   (Error handling: consider `catch_unwind` if the work function can panic, to prevent killing the worker thread. Log errors.)
    *   **Sub-Task 2.1.5: Implement `WorkQueue::schedule(&self, func: fn(data: usize), data: usize) -> Result<(), WorkQueueError>`:**
        *   Allocate `Box<WorkItem>` on the heap.
        *   Lock `self.task_list`.
        *   Push `work_item` to the back of the list.
        *   Unlock `self.task_list`.
        *   `self.work_pending_sem.release()` (signals the worker thread).
        *   Define `WorkQueueError` (e.g., `HeapAllocationFailed`).
    *   **Sub-Task 2.1.6: Global Work Queue Instance(s):**
        *   Decide on global work queue strategy:
            *   A single, system-wide, general-purpose work queue: `static KERNEL_GENERAL_WQ: OnceCell<Arc<WorkQueue>> = OnceCell::new();`
            *   Or, allow modules to create their own `WorkQueue` instances.
        *   If global, provide an `init_kernel_work_queues()` function called during `initcalls` to initialize `KERNEL_GENERAL_WQ`.
    *   **Sub-Task 2.1.7: ISR Usage Documentation/Example:**
        *   Provide a clear example of how an ISR would call `KERNEL_GENERAL_WQ.get().unwrap().schedule(...)`. Emphasize that `schedule` must be designed to be safe from ISR context (minimal work, no blocking).

**2.2. (Conceptual) Tasklet/Softirq Alternative - Kernel Timers for Deferred Work (No new sub-tasks unless this path is actively chosen over 2.1):**
    *   **Sub-Task 2.2.1: Evaluate Feasibility:** If a full work queue (2.1) is deemed too complex *for the very next step*, and the kernel timer system (from `init_dev_plan.md` 1.2.6, likely via `arch` and `drivers`) supports scheduling one-shot callbacks with arguments, this could be a temporary measure.
    *   **Sub-Task 2.2.2: Define API (if pursued):** e.g., `schedule_deferred(duration_ms: u64, func: fn(data: usize), data: usize)`.
    *   **Note:** This is generally less flexible and scalable than work queues. The plan should prioritize 2.1.


**2.3. SMP IRQ Controller Configuration (Arch Layer Focus):**
    *   **Goal:** Enable the `arch` layer to support routing specific hardware IRQs to designated CPUs or sets of CPUs. This primarily involves defining what the `interrupts` module expects from `arch`.
    *   **Location:** Primarily documentation within `interrupts_dev_plan.md` specifying requirements for `arch` module's `IrqControllerOps` or new `arch` functions.
    *   **Sub-Task 2.3.1: Refine `IrqControllerOps` for SMP (if needed):**
        *   Consider if methods like `enable`, `disable`, `ack`, `eoi` in `IrqControllerOps` (defined in Phase 1.1) need to take a CPU target or if they operate on the current CPU's controller interface. For I/O APICs, routing is distinct from these operations.
    *   **Sub-Task 2.3.2: Define `arch` API Requirements for Affinity:**
        *   Specify the expected signature and behavior for `fn arch_irq_set_affinity(hw_irq_num: u32, target_cpus: CpuMask, flags: IrqAffinityFlags) -> Result<(), ArchIrqError>` to be implemented by the `arch` module.
            *   `hw_irq_num`: The hardware IRQ number (e.g., GSI for I/O APIC).
            *   `CpuMask`: A type representing a set of CPU IDs (e.g., `bitflags` or `Vec<u32>`).
            *   `IrqAffinityFlags`: e.g., `PHYSICAL_DESTINATION_MODE`, `LOGICAL_DESTINATION_MODE`.
            *   This function would program I/O APIC redirection table entries or MSI/MSI-X message target registers.
    *   **Sub-Task 2.3.3: Initial Strategy for `interrupts` Module (Placeholder Logic):**
        *   Document that the `interrupts` module, during `request_irq` (Phase 1.2), if `CONFIG_SMP` is enabled but no specific affinity is requested by the driver:
            *   May initially default to routing all interrupts to the boot CPU (CPU0).
            *   Or, may implement a very simple round-robin distribution for newly requested IRQs among available CPUs using `arch_irq_set_affinity`.
        *   This is not full load balancing, just initial setup policy.


**2.4. Basic IRQ Affinity Placeholder in `IrqDescriptor`:**
    *   **Location:** `src/interrupts/mod.rs` (or where `IrqDescriptor` from Phase 1.1 is defined).
    *   **Sub-Task 2.4.1: Add Affinity Field to `IrqDescriptor`:**
        *   `struct IrqDescriptor { ... affinity: Option<CpuMask>, ... }`
        *   Initialize to `None` (no specific affinity).
    *   **Sub-Task 2.4.2: API to Set/Get Affinity (Conceptual):**
        *   Define placeholder functions like `pub fn set_irq_affinity(irq_num: u32, cpus: CpuMask) -> Result<()>` and `pub fn get_irq_affinity(irq_num: u32) -> Option<CpuMask>`.
        *   These functions would initially just store the `CpuMask` in the descriptor. The actual re-routing via `arch_irq_set_affinity` would be part of dynamic balancing in Phase 3.
        *   The `request_irq` function could optionally take an initial affinity.
---
### Phase 3: Full SMP IRQ Management & Advanced Features

**Goal:** Enhance the interrupt subsystem with Symmetric Multi-Processing (SMP) capabilities, including IRQ balancing, and introduce advanced features like threaded interrupt handlers, support for Message Signaled Interrupts (MSI/MSI-X), and improved diagnostics.

**Prerequisites:**

*   Phase 2 of `interrupts` module (Work Queues, `arch` support for basic affinity setting).
*   `tasks` module: Mature scheduler, kernel threads, synchronization primitives.
*   `mm` module: Reliable kernel heap, VMM.
*   `arch` module: IPIs (Inter-Processor Interrupts), per-CPU data support, specific controller logic for advanced MSI/MSI-X configuration and affinity.
*   (For 3.3) PCI subsystem (for device discovery and MSI/MSI-X configuration).
*   (For 3.4) VFS and a pseudo-filesystem like `procfs`.

**Key Modules/Files Involved:**

*   `src/interrupts/mod.rs`
*   `src/interrupts/smp.rs` (new, for balancing, affinity management)
*   `src/interrupts/threaded.rs` (new, for threaded IRQ management)
*   `src/interrupts/msi.rs` (new or expanded in `arch`)
*   `src/arch/[target_arch]/irq.rs`, `ioapic.rs`, `msi.rs`, `smp.rs`
*   `src/drivers/pci/mod.rs` (for MSI integration)
*   `src/fs/procfs/interrupts.rs` (new, for diagnostics)

**3.1. Dynamic IRQ Balancing:**
    *   **Goal:** Distribute interrupt load across CPUs to improve performance and reduce latency on SMP systems.
    *   **Sub-Task 3.1.1: Per-CPU IRQ Statistics:**
        *   Extend `IrqDescriptor` or create a new `PerCpuIrqStats` structure to count IRQ occurrences on each CPU.
        *   This requires `generic_irq_dispatcher` (from Phase 1.3) to identify the current CPU and update the correct counter. Access to per-CPU data will be via `arch` primitives.
        *   Protect stats with appropriate locks (e.g., per-CPU spinlocks or atomic increments if counters are per-CPU).
    *   **Sub-Task 3.1.2: `kirq_balance_d` Kernel Thread:**
        *   Create a kernel thread (e.g., `kirq_balance_d`) using `tasks::create_kernel_thread()`.
        *   This thread will run periodically (e.g., every few seconds, configurable).
        *   Its main loop will call a balancing function.
    *   **Sub-Task 3.1.3: IRQ Balancing Logic:**
        *   Implement `fn do_irq_balance()`:
            1.  Iterate through all managed IRQs (from `IRQ_DESCRIPTORS`).
            2.  For each IRQ, gather its per-CPU statistics.
            3.  Identify IRQs that are candidates for migration (not hard-affinitized by a driver, high load on one CPU while others are idle/less loaded for IRQs).
            4.  **Algorithm:** Implement a balancing algorithm (e.g., find most loaded CPU's busiest IRQ and least loaded CPU, then migrate if IRQ is migratable and target CPU is in its affinity mask if set).
            5.  If a migration is decided:
                *   Call `interrupts::set_irq_affinity(irq_num, new_target_cpu_mask)`.
    *   **Sub-Task 3.1.4: Implement `interrupts::set_irq_affinity(irq_num: u32, cpus: CpuMask) -> Result<()>` (Refined):**
        *   This function, prototyped in Phase 2.4, now performs the actual migration:
            1.  Get `IrqDescriptor`. Lock.
            2.  Temporarily disable the IRQ line using `controller_ops.disable()`.
            3.  Call `arch_irq_set_affinity(hw_irq_num, cpus, ...)` to reprogram the controller.
            4.  Update `IrqDescriptor.affinity` with the new `cpus` mask.
            5.  Re-enable the IRQ line using `controller_ops.enable()`.
            6.  Unlock descriptor.
        *   Consider IPIs to synchronize state if other CPUs might be handling this IRQ during reprogramming (though disabling it should prevent this).
    *   **Sub-Task 3.1.5: Throttling and Configuration:**
        *   Implement a mechanism to prevent too-frequent migrations (throttling).
        *   (Future) Expose tunables (e.g., via `sysfs` or `procfs`) for balancing parameters.
    *   **Sub-Task 3.1.6: Handling Non-Migratable IRQs:**
        *   Ensure the balancer respects IRQs that cannot or should not be moved (e.g., per-CPU timers, some specific device IRQs). This might be a flag in `IrqDescriptor` or determined by controller type.

**3.2. Threaded Interrupt Handlers (Full Implementation):**
    *   **Goal:** Allow complex or potentially blocking driver ISR logic to run in a schedulable kernel thread context, reducing time spent in true interrupt context.
    *   **Location:** `src/interrupts/threaded.rs`, `src/interrupts/mod.rs`.
    *   **Sub-Task 3.2.1: Define `ThreadedIrqData` and Refine `IrqAction`:**
        *   `struct ThreadedIrqData { dev_data: Box<dyn Any + Send>, irq_num: u32, sem: Arc<kernel::sync::Semaphore>, thread_pid: Option<tasks::PID> }` (or similar to pass context to the thread). `dev_data` is driver-specific.
        *   Modify `IrqAction` (from Phase 1.1) or create a new enum/struct to hold either a direct handler or a primary handler + threaded function details:
            ```rust
            // enum IrqHandlerType {
            //     Direct(fn(&TrapFrame, u32) -> IrqResult),
            //     Threaded {
            //         primary: Option<fn(&TrapFrame, u32, Arc<ThreadedIrqData>) -> IrqResult>,
            //         thread_fn: fn(Arc<ThreadedIrqData>) -> IrqResult,
            //         thread_data: Arc<ThreadedIrqData>,
            //     }
            // }
            // struct IrqAction { handler_type: IrqHandlerType, ... }
            ```
    *   **Sub-Task 3.2.2: Implement `request_threaded_irq(...)` API:**
        *   `pub fn request_threaded_irq(irq_num: u32, primary_handler: Option<...>, thread_fn: fn(...), flags: IrqFlags, name: &'static str, dev_data: Box<dyn Any + Send>) -> Result<(), IrqRequestError>`
        *   Creates `Arc<ThreadedIrqData>` containing `dev_data`, a new `Semaphore`, and placeholder for PID.
        *   Spawns a dedicated kernel thread for `thread_fn` using `tasks::create_kernel_thread()`. The thread will initially block on the semaphore. Store its PID in `ThreadedIrqData`.
        *   Constructs the `IrqAction` with `IrqHandlerType::Threaded`.
        *   Calls the existing `request_irq_internal` (or similar core registration logic from Phase 1.2) to add this action to the `IrqDescriptor`.
    *   **Sub-Task 3.2.3: Modify `generic_irq_dispatcher` (from Phase 1.3):**
        *   When an `IrqAction` with `IrqHandlerType::Threaded` is encountered:
            *   If `primary` handler exists, call it.
            *   If `primary` handler returns `IrqResult::WakeThread` (a new variant for `IrqResult`), or if no `primary` handler, then:
                *   `thread_data.sem.release()` to wake up the associated kernel thread.
            *   The main ISR path considers the IRQ handled at this point (for EOI purposes).
    *   **Sub-Task 3.2.4: Implement Kernel Thread Logic for `thread_fn`:**
        *   The thread function created by `request_threaded_irq` will loop:
            *   `thread_data.sem.acquire()`.
            *   Call the driver's `thread_fn(thread_data.clone())`.
            *   (Consider re-enabling the IRQ line at the controller if the primary handler disabled it, though level-triggered IRQs need careful management here).
    *   **Sub-Task 3.2.5: `free_threaded_irq(...)`:**
        *   Needs to remove the `IrqAction`, signal the thread to exit (e.g., via a flag in `ThreadedIrqData` and then releasing semaphore), and potentially join/wait for the thread to terminate (or let it exit and be cleaned up by `tasks` module). This is more complex than `free_irq` for direct handlers.

**3.3. Message Signaled Interrupts (MSI/MSI-X) Support:**
    *   **Goal:** Support modern PCI device interrupts, allowing devices to write to specific memory addresses to trigger interrupts, avoiding shared IRQ lines.
    *   **Sub-Task 3.3.1: `arch` Layer MSI/MSI-X Capabilities:**
        *   **Requirement for `arch`:**
            *   Functions to allocate/free blocks of interrupt vectors suitable for MSI/MSI-X.
            *   Functions to configure MSI/MSI-X hardware capabilities on a PCI device (e.g., number of vectors, enable MSI/MSI-X mode). This is often done via PCI configuration space writes.
            *   Functions to program the MSI message address and data for each vector on a device (target CPU, vector number).
            *   Mechanism for `arch` to map these incoming MSI messages (memory writes from devices) to internal kernel IRQ numbers or directly to IDT entries/handlers.
    *   **Sub-Task 3.3.2: `interrupts` Module Vector Management for MSI:**
        *   `fn alloc_msi_vectors(count: usize, preferred_cpu: Option<u32>) -> Result<Vec<MsiVectorInfo>, MsiError>`
            *   `MsiVectorInfo` contains `kernel_irq_num: u32`, `arch_specific_data_for_device_config` (e.g., message address/data to program into device).
            *   This function interacts with `arch` to reserve vectors and get necessary configuration data.
        *   `fn free_msi_vectors(vectors: Vec<MsiVectorInfo>)`
    *   **Sub-Task 3.3.3: `IrqDescriptor` Adaptation for MSI:**
        *   Determine how MSIs map to `IrqDescriptor`s. An MSI might translate to a standard `kernel_irq_num` that then uses the existing `IrqDescriptor` array.
        *   The `IrqControllerOps` might need an "MSI controller" variant or new methods if MSI handling (ack/eoi, enable/disable at source) is different. Often, MSIs are edge-triggered and don't need explicit EOI at a controller like I/O APIC. Disabling is often done at the device level.
    *   **Sub-Task 3.3.4: Integration with PCI Driver:**
        *   The PCI driver, upon discovering a device with MSI/MSI-X capability:
            1.  Calls `interrupts::alloc_msi_vectors()` to get vectors and config data.
            2.  Calls `arch` functions (or PCI config space accessors) to program the device's MSI/MSI-X registers with this data.
            3.  For each allocated MSI vector, calls `interrupts::request_irq(kernel_irq_num, ...)` to register the device driver's handler for that vector.
        *   On device removal, registered IRQs are freed, and `interrupts::free_msi_vectors()` is called.
    *   **Sub-Task 3.3.5: Handling Multiple MSI Messages (MSI-X):**
        *   Plan how MSI-X (multiple messages per device, each with its own vector and data/address) is handled. This typically means allocating a block of `kernel_irq_num`s for a single device.

**3.4. Advanced Diagnostics (`/proc/interrupts` or similar):**
    *   **Goal:** Provide a user-space readable interface for interrupt statistics.
    *   **Location:** `src/fs/procfs/interrupts.rs` (requires `procfs` to exist).
    *   **Sub-Task 3.4.1: Define `procfs` File Operations:**
        *   Implement `FileOps` for `/proc/interrupts`. The `read` operation will generate the content.
    *   **Sub-Task 3.4.2: Content Generation for `/proc/interrupts`:**
        *   The `read` operation should:
            1.  Print a header line with CPU names (e.g., "CPU0 CPU1 ...").
            2.  Iterate through `IRQ_DESCRIPTORS` (from Phase 1.1).
            3.  For each active IRQ:
                *   Print IRQ number.
                *   For each CPU, print the per-CPU count from `IrqDescriptor.statistics` (or `PerCpuIrqStats` from 3.1.1).
                *   Print interrupt controller name (from `IrqDescriptor.controller_ops` or a new field).
                *   Print device name(s) associated with the IRQ (from `IrqAction.dev_name`).
    *   **Sub-Task 3.4.3: Registration with `procfs`:**
        *   During `initcalls`, register the `/proc/interrupts` file with the `procfs` subsystem.
---
## Integration Points

*   **`init` Module:** Calls `interrupts::init_irq_system()` (Phase 1.1.1) after basic `arch` interrupt controller setup (IDT, PIC/APIC stubs from `init` Phase 1.2.3). CPU Exception handlers set by `init` or `arch` will be enhanced or replaced by those in this `interrupts` module (Phase 1.5).
*   **`arch` Module:**
    *   Provides low-level assembly stubs for all ISR vectors that call a common Rust entry point (e.g., `rust_irq_handler_entry` defined in this module).
    *   Implements `IrqControllerOps` for specific hardware (PIC, I/O APIC, LAPIC timer, MSI controller logic).
    *   Provides functions for enabling/disabling interrupts at the CPU level (e.g., `sti`, `cli`).
    *   Provides detailed CPU state (trap frames, control registers) for exception handlers.
    *   Provides IPI mechanisms for SMP (Phase 2/3).
*   **`drivers` Module:** Device drivers are the primary clients of `request_irq()`, `free_irq()`, `enable_irq_line()`, `disable_irq_line()`. Driver ISRs are registered here.
*   **`tasks` Module:**
    *   Deferred work mechanisms (Phase 2) will likely create/use kernel threads from `tasks`.
    *   ISRs might wake tasks blocked on I/O (using synchronization primitives provided by or integrated with `tasks`).
    *   Task context (e.g., if a user-space task caused a CPU exception) is relevant for advanced exception handlers.
    *   Threaded IRQs (Phase 3) directly create and manage kernel threads.
*   **`mm` Module:** The Page Fault (#PF) handler is a CPU exception but is primarily managed by the `mm` module due to its complexity and tight coupling with memory structures. Other CPU exceptions (e.g., #GP if caused by bad memory access not caught by #PF) will be handled here, possibly logging info from `mm`.
*   **Kernel Core / Panic Handling:** Advanced exception handlers will invoke `panic()` upon unrecoverable kernel-mode faults, providing more detailed information.


[end of src/interrupts/interrupts_dev_plan.md]

[end of src/interrupts/interrupts_dev_plan.md]

