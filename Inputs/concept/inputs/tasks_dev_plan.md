# Scheduler & Process Management (`tasks`) Development Plan

## Overview

This document outlines the development plan for the Scheduler and Process Management module, named `tasks`, for GritOS. This module is fundamental for enabling concurrent execution of kernel threads and, eventually, user-space processes. It will manage task lifecycles, scheduling policies, context switching, and basic synchronization primitives. A robust and efficient `tasks` module is critical for a responsive and stable operating system.

## Responsibilities

The `tasks` module will be responsible for:

*   **Task/Process Representation:** Defining and managing Task Control Blocks (TCBs) or Process Control Blocks (PCBs) that store all necessary information about an executable entity (kernel thread or user process).
*   **Scheduling:** Implementing scheduling algorithms to determine which task runs next on each CPU. This includes managing run queues and task states (e.g., Ready, Running, Blocked).
*   **Lifecycle Management:**
    *   Creating new tasks (kernel threads initially, then user processes).
    *   Terminating tasks and cleaning up their resources.
    *   (Future) Supporting parent/child relationships and process waiting (`fork`, `exec`, `exit`, `wait`).
*   **Context Switching:** Performing the architecture-specific operations to save the context of the currently running task and restore the context of the next task to run.
*   **Synchronization Primitives (Basic):** Providing foundational, scheduler-aware synchronization primitives (e.g., mutexes, semaphores that can block and unblock tasks). Spinlocks are typically lower-level and might reside in an `arch` or `sync` module but are used by the scheduler.
*   **Signal Handling (Rudimentary):** (Future) Basic infrastructure for delivering signals to processes.
*   **Inter-Process Communication (IPC) Foundations:** (Future) Laying groundwork for basic IPC mechanisms.
*   **SMP Considerations:** (Future) Ensuring scheduler and task management are safe and efficient on multi-processor systems, including per-CPU run queues and load balancing.

## Inspiration from Linux (kernel/sched/, kernel/fork.c, etc.)

The design will draw inspiration from Linux's scheduler and process management components:

*   **Task Structure:** `struct task_struct` in `include/linux/sched.h` for TCB/PCB concepts.
*   **Scheduling Core:** `kernel/sched/core.c`, `kernel/sched/fair.c` (CFS), `kernel/sched/rt.c`, `kernel/sched/deadline.c` for scheduling algorithms and run queue management.
*   **Process Lifecycle:** `kernel/fork.c` (`do_fork`), `kernel/exit.c` (`do_exit`), `kernel/exec_domain.c` (for `execve`).
*   **Context Switching:** Architecture-specific context switch code (e.g., `arch/x86/entry/common.c`, `arch/x86/kernel/process.c`).
*   **Synchronization:** `kernel/locking/`, `include/linux/mutex.h`, `include/linux/semaphore.h`.
*   **Signals:** `kernel/signal.c`.

While GritOS will start simpler, these provide proven concepts for a robust system.

## Rust Implementation Strategy

*   **Safety and Abstraction:** Leverage Rust's safety features (ownership, lifetimes, type system) to manage complex state and concurrency. Use traits for abstracting scheduler policies or TCB/PCB types if multiple are needed.
*   **TCB/PCB Design:** `Task` or `Process` structs will hold all state. `Arc<Mutex<Task>>` or similar for shared, mutable access where appropriate.
*   **Modularity:**
    *   Scheduler policies (e.g., round-robin, priority-based) could be pluggable implementations of a `SchedulerPolicy` trait.
    *   Context switching logic will be heavily reliant on `arch` module specifics.
*   **Concurrency:** Use spinlocks for fine-grained locking of scheduler queues and TCB fields. Scheduler-aware mutexes/semaphores for longer-held locks that allow tasks to sleep.
*   **Error Handling:** Use `Result` for operations that can fail (e.g., task creation if out of memory/PIDs). Define specific error types.
*   **Atomic Operations:** Utilize atomic types for counters (PIDs, statistics) and flags where appropriate.

## Key Components

### A. Task Control Block (TCB) / Process Control Block (PCB)
*   **Purpose:** Represents a single thread of execution (kernel thread or user process).
*   **Details:** Rust struct (`Task` or `Process`) containing: PID, state, priority, scheduling info (timeslice, run queue pointers), memory management info (pointer to `AddressSpace` from `mm` module), kernel stack pointer, saved register context, parent/child info, signal mask/pending signals, open file table (future).

### B. Scheduling Framework & Algorithms
*   **Purpose:** Manages ready-to-run tasks and selects which one to execute next.
*   **Details:**
    *   `Scheduler` trait: Defines common operations like `add_task`, `remove_task`, `pick_next_task`.
    *   Run Queue: Data structure(s) to hold ready tasks (e.g., `VecDeque` for round-robin, priority queues for priority scheduling).
    *   Initial Algorithm: Round-robin.
    *   (Future) Priority-based, CFS-like.

### C. Process Lifecycle Management
*   **Purpose:** Functions to create, manage, and terminate tasks/processes.
*   **Details:**
    *   Kernel thread creation (`kthread_create`).
    *   (Future) `fork`, `execve`, `exit`, `wait` system call implementations or internal kernel equivalents.

### D. Context Switching
*   **Purpose:** The mechanism to switch CPU execution from one task to another.
*   **Details:** Architecture-specific assembly routines (`switch_to`) called by higher-level scheduler code. Manages saving/restoring GPRs, stack pointer, instruction pointer, CR3 (page table base), FPU/SIMD state.

### E. Synchronization Primitives (Scheduler-Aware)
*   **Purpose:** Basic mechanisms for tasks to synchronize and wait for resources.
*   **Details:**
    *   Scheduler-aware Mutexes/Semaphores: Allow a task to block if a lock is unavailable, placing it on a wait queue associated with the lock, and waking it when the lock is released.
    *   Wait Queues: Data structures for tasks blocked on a specific event or resource.

### F. Signal Handling (Rudimentary - Future Phase)
*   **Purpose:** Basic infrastructure for delivering asynchronous notifications (signals) to processes.
*   **Details:** TCB fields for pending/masked signals. Kernel entry/exit points for signal delivery.

### G. Inter-Process Communication (IPC) Foundations (Rudimentary - Future Phase)
*   **Purpose:** Basic mechanisms for processes to communicate.
*   **Details:** (e.g., pipes, wait queues for more complex primitives).

## Phased Development

### Phase 1: Basic Kernel Thread Scheduling & Context Switching
This phase focuses on getting multiple kernel threads running concurrently with a simple scheduler.
*   **1.1. Task Control Block (TCB) Definition for Kernel Threads:**
    *   **Data Structure:** Define `struct Task` (or `KThread`) in `src/tasks/tcb.rs` (or `process.rs`).
        *   `pid: PID` (Unique Process ID, from an atomic counter).
        *   `name: &'static str` (for debugging).
        *   `state: TaskState` (enum: `Ready`, `Running`, `Blocked`, `Zombie`).
        *   `kernel_stack: VirtAddr` (pointer to top of kernel stack).
        *   `kernel_stack_size: usize`.
        *   `context: arch::Context` (architecture-specific register state, including RSP, RIP, RFLAGS, CR3).
        *   `page_table_root: PhysAddr` (physical address of the PML4/top-level page table; initially kernel's for kernel threads).
        *   (Future) `priority: u8`, `timeslice_remaining: u32`.
        *   `run_queue_node: Option<LinkedListNode<Arc<Task>>>` (or similar for scheduler's run queue).
    *   **Initialization:** `Task::new_kernel_thread(entry_point: fn() -> !, stack_size: usize, name: &'static str, pmm: &mut dyn FrameAllocator, vmm: &KernelVmm) -> Result<Arc<Mutex<Task>>>`.
        *   Allocate PID.
        *   Allocate kernel stack (e.g., using `pmm.allocate_contiguous_frames` and `vmm.map_kernel_page` into a kernel stack area).
        *   Initialize `arch::Context` with `entry_point` as RIP, stack pointer set up, kernel CS, interrupts enabled in RFLAGS. CR3 will be kernel's.
*   **1.2. Architecture-Specific Context Switching Implementation:**
    *   **Interface Definition (in `src/arch/[arch]/context.rs`):**
        *   `struct Context { /* registers like rsp, rip, rflags, cr3, gprs etc. */ }`
        *   `unsafe fn switch_context(from_context: *mut Context, to_context: *const Context)`
    *   **Assembly Routine (e.g., `src/arch/x86_64/switch.S`):**
        *   `switch_context_asm`: Saves all callee-saved registers of current task, saves current RSP into `from_context->rsp`, loads new RSP from `to_context->rsp`, restores callee-saved registers of new task, loads new CR3 from `to_context->cr3` (if different and needed), `ret` (which pops RIP from new stack).
        *   Ensure proper calling convention and parameter passing from Rust.
*   **1.3. Basic Scheduler Implementation (Round-Robin):**
    *   **Scheduler Trait (in `src/tasks/scheduler.rs`):**
        *   `trait Scheduler { fn add_task(&mut self, task: Arc<Mutex<Task>>); fn pick_next_task(&mut self) -> Option<Arc<Mutex<Task>>>; fn remove_task(&mut self, task_pid: PID); ... }`
    *   **RoundRobinScheduler Implementation:**
        *   `ready_queue: VecDeque<Arc<Mutex<Task>>>`.
        *   `add_task`: Pushes to back of queue.
        *   `pick_next_task`: Pops from front. If task was picked, it's not re-added here (done by `schedule()` after it runs).
    *   **Global Scheduler Instance:** `static KERNEL_SCHEDULER: Lazy<Mutex<Box<dyn Scheduler + Send>>> = Lazy::new(...)`.
*   **1.4. Core `schedule()` Function:**
    *   **Location:** `src/tasks/scheduler.rs`.
    *   `pub fn schedule()`:
        1.  Disable interrupts (`arch::interrupts::disable()`).
        2.  Get current running task (`CURRENT_TASK: static AtomicPtr<Task>`).
        3.  If current task exists and was `Running`:
            *   Save its context (partially done by interrupt entry if called from timer).
            *   Set its state to `Ready`.
            *   Add it back to scheduler's ready queue.
        4.  Call `KERNEL_SCHEDULER.lock().pick_next_task()`.
        5.  If a `next_task` is chosen:
            *   Set `next_task.state` to `Running`.
            *   Update `CURRENT_TASK` to point to `next_task`.
            *   `unsafe { arch::context_switch(&mut old_task_context_ptr, &next_task_context_ptr) }`.
        6.  Else (no task to run, should only be idle task if implemented): Re-enable interrupts and potentially halt or run idle logic.
        *   (Note: Interrupts re-enabled by `iretq` or explicitly if no switch occurs).
*   **1.5. Kernel Thread Creation API:**
    *   `pub fn create_kernel_thread(name: &'static str, entry: extern "C" fn(arg: *mut u8) -> !, arg: *mut u8) -> Result<PID, CreateTaskError>`
        *   Uses `Task::new_kernel_thread` to create TCB and kernel stack.
        *   Sets up entry point and argument on the new stack so `entry(arg)` is called when switched to.
        *   Adds new task to scheduler: `KERNEL_SCHEDULER.lock().add_task(new_task)`.
*   **1.6. Idle Task(s) Implementation:**
    *   `fn idle_thread_entry() -> ! { loop { arch::wait_for_interrupt(); /* or hlt */ tasks::schedule(); /* ensure scheduler runs if tasks became ready */ } }`
    *   Create one idle task per CPU (for future SMP) using `create_kernel_thread`.
*   **1.7. Integration with Timer Interrupt for Preemption:**
    *   Timer IRQ handler (from `init` plan 1.2.6) calls a scheduler tick function.
    *   `scheduler::tick()`: Decrement current task's timeslice. If timeslice expires, set a flag `NEED_RESCHEDULE = true`.
    *   Modify IRQ return path: If `NEED_RESCHEDULE` is true, call `schedule()` before `iretq`.
*   **1.8. Initial Bootstrap & First Context Switch:**
    *   In `kernel_init` (after MM and basic scheduler structures are up):
        1.  Create idle task(s).
        2.  Create an initial test kernel thread (e.g., `test_kthread_entry`).
        3.  Set `CURRENT_TASK` to this test thread. Mark it as `Running`.
        4.  Set up a dummy "bootstrap" TCB representing the current `kernel_init` context.
        5.  Call `arch::context_switch` from the bootstrap TCB to the test kernel thread. `kernel_init` effectively pauses until this thread yields or blocks.
*   **1.9. Yielding Mechanism:**
    *   `pub fn sched_yield()`: Explicitly calls `schedule()`.

### Phase 2: User Process Creation & Lifecycle Support
*   **2.1. Extend Task Control Block (TCB) for User Processes:**
    *   **New Fields in `Task` struct (or a new `UserProcess` struct):**
        *   `user_stack_pointer: Option<VirtAddr>` (if different from kernel stack pointer handling during user->kernel transitions).
        *   `user_heap_info: Option<UserHeapInfo>` (start, end, current break for `sbrk`).
        *   `parent_pid: Option<PID>`.
        *   `children: Mutex<Vec<PID>>`.
        *   `exit_code: Option<i32>`.
        *   `credentials: UserCredentials { uid: u32, gid: u32, euid: u32, egid: u32, ... }`.
        *   `signal_handlers: Arc<SignalHandlers>` (table of signal dispositions).
        *   `pending_signals: SigSet`.
        *   `blocked_signals: SigSet`.
        *   `fd_table: Arc<Mutex<FileDescriptorTable>>`.
        *   `address_space: Arc<mm::AddressSpace>` (from `mm` module).
    *   **Consider `Arc<AddressSpace>` carefully:** Ensure it integrates with `mm`'s plans for `AddressSpace` management, especially `fork` (CoW) and `exec`.
*   **2.2. `fork()` Implementation:**
    *   **Syscall Entry:** Define `sys_fork()`.
    *   **Core Logic:**
        1.  Get current (parent) process.
        2.  Allocate new PID for child.
        3.  Duplicate parent's `Task` structure:
            *   Copy relevant fields (credentials, signal handlers, FD table - with appropriate `Arc` bumps or duplication).
            *   Set child's state to `Ready`.
            *   Clear pending signals for child.
        4.  Duplicate parent's `AddressSpace` using `mm::AddressSpace::duplicate_for_fork()` (this handles CoW setup for memory). Assign to child task.
        5.  Allocate new kernel stack for child.
        6.  Set up child's `arch::Context` so it starts returning from `fork()` in child context (e.g., return value 0). Parent's context set to return child's PID.
        7.  Add child to parent's `children` list. Set child's `parent_pid`.
        8.  Add child task to scheduler.
        9.  Return child PID to parent, 0 to child.
    *   **Error Handling:** `ENOMEM` if resources (PID, memory, TCB) exhausted.
*   **2.3. `execve()` Implementation:**
    *   **Syscall Entry:** Define `sys_execve(pathname: &CStr, argv: &[&CStr], envp: &[&CStr])`.
    *   **Core Logic:**
        1.  Get current process.
        2.  Copy `pathname`, `argv`, `envp` from user space to kernel space. Validate.
        3.  Open executable file via VFS (`vfs::kernel_lookup_path`, check permissions).
        4.  Parse executable format (e.g., ELF loader) to get entry point, segment info.
        5.  Call `mm::AddressSpace::reset_for_exec()` on current process's address space. This will:
            *   Unmap all existing user VMAs.
            *   Create new VMAs for code, data, BSS based on executable.
            *   Set up user stack VMA.
        6.  Copy `argv` and `envp` to the new user stack.
        7.  Modify current task's `arch::Context` to set RIP to executable's entry point, RSP to new user stack pointer.
        8.  (Important) Update task name, clear some signal state, reset FD flags (e.g., `FD_CLOEXEC`).
        9.  Return from syscall (will effectively jump to new program's entry). This does not return on success.
    *   **Error Handling:** `ENOENT`, `EACCES`, `ENOMEM`, `EINVAL` (bad format).
*   **2.4. `exit()` Implementation:**
    *   **Syscall Entry:** Define `sys_exit(status: i32)`.
    *   **Core Logic (`do_exit`):**
        1.  Get current process.
        2.  Set task state to `Zombie`. Store `exit_code`.
        3.  Release most resources:
            *   Close all open file descriptors (decrement refcounts).
            *   Drop `Arc<AddressSpace>` (MM will handle VMA/page table cleanup via its `Drop` impl).
            *   Detach from terminal, session, process group (future).
        4.  Reparent any children to PID 1 (or a subreaper).
        5.  Send `SIGCHLD` to parent.
        6.  Call `schedule()` to switch to another task (current task will not run again).
*   **2.5. `waitpid()` / `wait()` Implementation (Basic):**
    *   **Syscall Entry:** Define `sys_waitpid(pid: PID, status_ptr: UserOutPtr<i32>, options: WaitOptions)`.
    *   **Core Logic:**
        1.  Get current process (parent).
        2.  Check children:
            *   If `pid` specifies a child that is a `Zombie`: Collect its `exit_code`, deallocate its TCB and kernel stack, remove from parent's children list. Write status to `status_ptr`. Return child PID.
            *   If no zombie children match and `WNOHANG` is not set: Block current task (`TaskState::Blocked`) and add to a wait queue associated with its children or a specific child. Call `schedule()`.
            *   When woken (by `exit()` of a child sending `SIGCHLD` or directly waking parent): Re-check for zombie children.
    *   **Error Handling:** `ECHILD` (no such child, or no children to wait for).

### Phase 3: Advanced Scheduling Features & Basic IPC
*   **3.1. Priority-Based Scheduling:**
    *   **TCB Extension:** Add `priority: u8` and `static_priority: u8` to `Task`.
    *   **Scheduler Policy:**
        *   Implement a `PriorityScheduler` (or extend `RoundRobinScheduler`).
        *   Use multiple ready queues (one per priority level) or a priority heap.
        *   `pick_next_task` selects from highest-priority non-empty queue.
    *   **Priority Adjustment:** Syscalls like `nice()` or `setpriority()` (future). Dynamic priority boosting for I/O bound tasks.
    *   **Preemption:** High-priority task becoming ready should preempt a lower-priority running task.
*   **3.2. Scheduler-Aware Synchronization Primitives:**
    *   **Mutex (Blocking):**
        *   `struct KernelMutex { locked: AtomicBool, owner: Option<PID>, wait_queue: WaitQueue }`.
        *   `lock()`: If locked by another task, add current task to `wait_queue`, set state to `Blocked`, call `schedule()`.
        *   `unlock()`: If wait queue not empty, wake one task. Set its state to `Ready`, add to scheduler.
    *   **Semaphore (Blocking):**
        *   `struct Semaphore { count: AtomicIsize, wait_queue: WaitQueue }`.
        *   `wait()` (`P` operation): Decrement count. If < 0, block current task on `wait_queue`.
        *   `signal()` (`V` operation): Increment count. If <= 0 (meaning tasks were waiting), wake one task from `wait_queue`.
    *   **Wait Queues:**
        *   `struct WaitQueue { queue: LinkedList<Arc<Mutex<Task>>> }` (or similar).
        *   `wait_on(event_condition)`: Atomically check condition, if false, add current task to queue, block.
        *   `wake(queue, all=false)`: Wake one/all tasks on queue.
*   **3.3. Basic Signal Handling Infrastructure:**
    *   **TCB Fields:** `pending_signals: SigSet`, `blocked_signals: SigSet`, `signal_actions: [SignalAction; NSIG]`.
    *   `SigSet`: Bitmask for signals.
    *   `SignalAction`: Enum (Ignore, Terminate, CoreDump, Stop, Continue, UserHandler(VirtAddr)).
    *   **`sys_kill(pid: PID, sig: Signal)`:** Send signal `sig` to process `pid`. Add to `pending_signals`. If target task is interruptible and not blocking the signal, wake it and mark for signal delivery.
    *   **Signal Delivery (on return to user mode or interruptible kernel sleep):**
        *   Check pending unblocked signals.
        *   If user handler: Modify user trap frame to jump to handler, set up signal stack frame.
        *   If default action: Terminate, stop, etc.
    *   `sys_sigaction`, `sys_sigprocmask` (future, for full POSIX signals).
*   **3.4. Pipes (Anonymous IPC):**
    *   **Data Structure:** `struct Pipe { buffer: RingBuffer<u8>, read_end_open: AtomicBool, write_end_open: AtomicBool, read_wait_queue: WaitQueue, write_wait_queue: WaitQueue }`.
    *   **`sys_pipe(fds: UserOutPtr<[i32; 2]>)`:** Create a pipe, return two file descriptors (FDs).
    *   **File Operations:** Implement `read` and `write` for pipe FDs.
        *   `read`: If buffer empty and write end open, block on `read_wait_queue`.
        *   `write`: If buffer full and read end open, block on `write_wait_queue`.
        *   Handle EOF/SIGPIPE if ends are closed.
    *   Integrate with VFS by creating `Inode`s for pipes and specific `FileOps`.

### Phase 4: Symmetric Multi-Processing (SMP) Scheduling Considerations
*   **4.1. Per-CPU Data and Run Queues:**
    *   **`PerCpuData` struct:** `current_task: Option<Arc<Mutex<Task>>>`, `local_run_queue: SchedulerPolicyInstance`, `cpu_id: u32`, `scheduler_lock: SpinLock`.
    *   Access via `GS`/`FS` segment register or dedicated `arch` functions.
    *   Each CPU's scheduler lock protects its own run queue and current task pointer.
*   **4.2. Locking Strategy for Global Scheduler Structures:**
    *   `TASK_REGISTRY` needs SMP-safe locking (e.g., `Mutex` or fine-grained locking if it becomes a bottleneck).
    *   PID allocator must be atomic.
*   **4.3. Task Migration and Load Balancing:**
    *   **Affinity:** TCB field for CPU affinity (soft or hard).
    *   **Load Balancing Algorithm:**
        *   Triggered periodically by a timer on one CPU, or by CPUs when their run queue is empty.
        *   Pulls tasks from busier CPUs' run queues to its own.
        *   Requires inter-CPU communication (IPIs) to safely transfer tasks.
    *   **Task Migration Logic:**
        *   Safely stop task on source CPU (if running).
        *   Remove from source run queue (with lock).
        *   Update TCB's CPU ID / affinity data.
        *   Add to destination run queue (with lock).
        *   Potentially send IPI to destination CPU if it's idle and the migrated task is high priority.
*   **4.4. Inter-Processor Interrupts (IPIs) for Scheduling:**
    *   `ipi_reschedule(target_cpu: u32)`: Sends an IPI to `target_cpu`.
    *   IPI Handler for Reschedule: Sets `NEED_RESCHEDULE` flag on the target CPU, which then calls `schedule()` on IRQ return.
    *   Used for: Waking a task that should run on another CPU, load balancing.
*   **4.5. Context Switching & TLB in SMP:**
    *   Context switch remains largely per-CPU.
    *   TLB shootdown: If a task is migrated or its address space is modified (e.g., `munmap`) while it might have run on another CPU, IPIs might be needed to tell other CPUs to flush their TLB entries for that address space. This is complex and depends on `mm`'s handling of shared address spaces.
*   **4.6. Synchronization Primitive Scalability:**
    *   Evaluate spinlock implementations for fairness and performance on SMP (e.g., ticket locks vs. test-and-set).
    *   Ensure scheduler-aware mutexes/semaphores correctly handle waking tasks on potentially different CPUs.

## Integration Points

*   **`init` Module:** Calls `tasks::init_scheduler_subsystem()` (or similar) via initcalls to set up the scheduler, create initial kernel threads (idle, main kernel init thread). The main kernel init thread (spawned by `tasks` module) continues kernel initialization.
*   **`mm` (Memory Management) Module:**
    *   Allocates kernel stacks for tasks.
    *   Manages `AddressSpace` (page tables, VMAs) for each user process; TCBs will hold an `Arc<mm::AddressSpace>`.
    *   Context switch involves telling `mm` to activate a new address space (e.g., load new CR3).
*   **`arch` Module:**
    *   Provides architecture-specific context switching routines (`arch::Context`, `arch::switch_context`).
    *   Provides details for interrupt/exception frames needed for signal delivery and user mode transitions.
    *   Manages enabling/disabling interrupts for critical scheduler sections.
    *   Provides IPI mechanisms for SMP.
    *   Provides per-CPU data access mechanisms.
*   **`interrupts` Module / CPU Exception Handling:**
    *   Timer interrupt triggers scheduler ticks and preemption logic.
    *   Syscall interrupt transfers control from user to kernel, potentially interacting with scheduler for blocking operations or process termination.
    *   Page faults (handled by `mm`) can interact with task state if a fault is unrecoverable for a user process.
*   **Syscall Layer (Future):** `fork`, `execve`, `exit`, `waitpid`, `sched_yield`, signal-related syscalls, IPC syscalls will all be primary entry points into the `tasks` module.
*   **Drivers (Future):** Drivers will use scheduler-aware synchronization primitives to block tasks waiting for I/O and will subsequently wake them upon I/O completion.
