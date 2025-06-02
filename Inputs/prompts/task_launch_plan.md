# gRiTOS Task Launch Plan

## Introduction

This document provides a recommended launch order for the development tasks outlined in the gRiTOS Development Plan (`/plan/gritos_dev_plan.md`). The tasks are generated from Section 3 ("Core Modules & Development Sub-Plans") and are grouped here by the high-level development phases defined in Section 4 of the main plan.

Each task listing includes:
*   A descriptive name for the task.
*   The full path to its corresponding prompt file in the `/prompts` directory.
*   A copy-paste helper for initial launch.
*   A copy-paste helper for retry if a previous instance crashed.

This plan aims to prioritize foundational tasks and manage dependencies to facilitate smoother development.

---

## Phase 0: Foundation & Planning

*(This phase is largely covered by the creation of the gRiTOS Development Plan itself and initial setup. No specific generated prompts correspond to this phase directly, as those would be meta-tasks like "Define Product Specification" which are already inputs.)*

---

## Phase 1: Core Kernel Boot and Minimal IPC

**Goal:** Boot a minimal kernel on the reference hardware/emulator. Implement basic kernel objects, memory management (static allocation, UntypedMemory retyping for kernel structures), scheduler, and a functional IPC mechanism for kernel-level communication.

### Foundation & Bootloader (Module 3.1)
*   **Task:** Design and implement ROM Bootloader (RBL) / Primary Bootloader (PBL) verification logic.
    *   **Prompt File:** `/prompts/M3_S1_T1_Implement_RBL_PBL_Verification_Logic.txt`
    *   **Initial Launch:** `/prompts/M3_S1_T1_Implement_RBL_PBL_Verification_Logic.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S1_T1_Implement_RBL_PBL_Verification_Logic.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Develop Secondary Bootloader (SBL) with hardware initialization capabilities.
    *   **Prompt File:** `/prompts/M3_S1_T2_Develop_SBL_With_HW_Init.txt`
    *   **Initial Launch:** `/prompts/M3_S1_T2_Develop_SBL_With_HW_Init.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S1_T2_Develop_SBL_With_HW_Init.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Implement cryptographic verification (hashing, signature check) in SBL for the kernel image.
    *   **Prompt File:** `/prompts/M3_S1_T3_Implement_SBL_Kernel_Verification.txt`
    *   **Initial Launch:** `/prompts/M3_S1_T3_Implement_SBL_Kernel_Verification.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S1_T3_Implement_SBL_Kernel_Verification.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Define and implement boot information structure passed from SBL to kernel.
    *   **Prompt File:** `/prompts/M3_S1_T5_Define_SBL_Kernel_Boot_Info.txt`
    *   **Initial Launch:** `/prompts/M3_S1_T5_Define_SBL_Kernel_Boot_Info.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S1_T5_Define_SBL_Kernel_Boot_Info.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

### HAL & BSP (Module 3.6) - Core HAL for Boot
*   **Task:** Define HAL API for CPU architecture specifics, core peripherals, and boot interface.
    *   **Prompt File:** `/prompts/M3_S6_T1_Define_HAL_API.txt`
    *   **Initial Launch:** `/prompts/M3_S6_T1_Define_HAL_API.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S6_T1_Define_HAL_API.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Implement HAL for the primary reference architecture.
    *   **Prompt File:** `/prompts/M3_S6_T2_Implement_HAL_For_Reference_Arch.txt`
    *   **Initial Launch:** `/prompts/M3_S6_T2_Implement_HAL_For_Reference_Arch.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S6_T2_Implement_HAL_For_Reference_Arch.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

### Kernel Core (Module 3.2) - Initial Setup
*   **Task:** Implement static memory allocation for all kernel data structures.
    *   **Prompt File:** `/prompts/M3_S2_T4_Implement_Static_Kernel_Memory_Allocation.txt`
    *   **Initial Launch:** `/prompts/M3_S2_T4_Implement_Static_Kernel_Memory_Allocation.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S2_T4_Implement_Static_Kernel_Memory_Allocation.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Implement core kernel objects (Capability, CNode, TCB, Endpoint, Notification, AddressSpace, UntypedMemory, IRQ Handler Object, Memory Object).
    *   **Prompt File:** `/prompts/M3_S2_T2_Implement_Core_Kernel_Objects.txt`
    *   **Initial Launch:** `/prompts/M3_S2_T2_Implement_Core_Kernel_Objects.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S2_T2_Implement_Core_Kernel_Objects.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Define and implement the minimal System Call Interface.
    *   **Prompt File:** `/prompts/M3_S2_T1_Implement_Syscall_Interface.txt`
    *   **Initial Launch:** `/prompts/M3_S2_T1_Implement_Syscall_Interface.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S2_T1_Implement_Syscall_Interface.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Implement `gRiTOS_KernelObject_Invoke` methods for each kernel object type.
    *   **Prompt File:** `/prompts/M3_S2_T3_Implement_KernelObject_Invoke_Methods.txt`
    *   **Initial Launch:** `/prompts/M3_S2_T3_Implement_KernelObject_Invoke_Methods.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S2_T3_Implement_KernelObject_Invoke_Methods.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Design and implement capability system.
    *   **Prompt File:** `/prompts/M3_S2_T11_Implement_Capability_System.txt`
    *   **Initial Launch:** `/prompts/M3_S2_T11_Implement_Capability_System.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S2_T11_Implement_Capability_System.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Develop deterministic context switching mechanisms. (HAL Task, but critical for scheduler)
    *   **Prompt File:** `/prompts/M3_S2_T7_Develop_Deterministic_Context_Switching.txt`
    *   **Initial Launch:** `/prompts/M3_S2_T7_Develop_Deterministic_Context_Switching.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S2_T7_Develop_Deterministic_Context_Switching.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Implement preemptive, priority-based scheduler.
    *   **Prompt File:** `/prompts/M3_S2_T5_Implement_Preemptive_Scheduler.txt`
    *   **Initial Launch:** `/prompts/M3_S2_T5_Implement_Preemptive_Scheduler.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S2_T5_Implement_Preemptive_Scheduler.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Implement TCB management.
    *   **Prompt File:** `/prompts/M3_S2_T8_Implement_TCB_Management.txt`
    *   **Initial Launch:** `/prompts/M3_S2_T8_Implement_TCB_Management.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S2_T8_Implement_TCB_Management.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

### Memory Management Subsystem (Module 3.4) - Initial Kernel MM
*   **Task:** Implement MMU/MPU configuration and management for memory isolation.
    *   **Prompt File:** `/prompts/M3_S4_T1_Implement_MMU_MPU_Configuration.txt`
    *   **Initial Launch:** `/prompts/M3_S4_T1_Implement_MMU_MPU_Configuration.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S4_T1_Implement_MMU_MPU_Configuration.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Implement `UntypedMemory` object and `Untyped_Retype` method.
    *   **Prompt File:** `/prompts/M3_S4_T2_Implement_UntypedMemory_Retype.txt`
    *   **Initial Launch:** `/prompts/M3_S4_T2_Implement_UntypedMemory_Retype.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S4_T2_Implement_UntypedMemory_Retype.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

### IPC Subsystem (Module 3.3) - Core IPC Primitives
*   **Task:** Define and implement IPC message structure (`msg_info`).
    *   **Prompt File:** `/prompts/M3_S3_T3_Define_IPC_Message_Structure.txt`
    *   **Initial Launch:** `/prompts/M3_S3_T3_Define_IPC_Message_Structure.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S3_T3_Define_IPC_Message_Structure.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Implement Endpoint kernel object logic.
    *   **Prompt File:** `/prompts/M3_S3_T6_Implement_Endpoint_Object_Logic.txt`
    *   **Initial Launch:** `/prompts/M3_S3_T6_Implement_Endpoint_Object_Logic.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S3_T6_Implement_Endpoint_Object_Logic.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Implement Notification kernel object logic.
    *   **Prompt File:** `/prompts/M3_S3_T7_Implement_Notification_Object_Logic.txt`
    *   **Initial Launch:** `/prompts/M3_S3_T7_Implement_Notification_Object_Logic.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S3_T7_Implement_Notification_Object_Logic.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Implement synchronous IPC (`gRiTOS_Call`, `gRiTOS_Reply`).
    *   **Prompt File:** `/prompts/M3_S3_T1_Implement_Synchronous_IPC.txt`
    *   **Initial Launch:** `/prompts/M3_S3_T1_Implement_Synchronous_IPC.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S3_T1_Implement_Synchronous_IPC.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Implement secure capability transfer via IPC.
    *   **Prompt File:** `/prompts/M3_S3_T4_Implement_Secure_Capability_Transfer.txt`
    *   **Initial Launch:** `/prompts/M3_S3_T4_Implement_Secure_Capability_Transfer.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S3_T4_Implement_Secure_Capability_Transfer.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Implement asynchronous IPC (`gRiTOS_SendAsync`, Notification signals/waits).
    *   **Prompt File:** `/prompts/M3_S3_T2_Implement_Asynchronous_IPC.txt`
    *   **Initial Launch:** `/prompts/M3_S3_T2_Implement_Asynchronous_IPC.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S3_T2_Implement_Asynchronous_IPC.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

### Kernel Core (Module 3.2) - Finalizing Phase 1 Kernel Logic
*   **Task:** Implement process management.
    *   **Prompt File:** `/prompts/M3_S2_T9_Implement_Process_Management.txt`
    *   **Initial Launch:** `/prompts/M3_S2_T9_Implement_Process_Management.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S2_T9_Implement_Process_Management.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Implement kernel interrupt handling.
    *   **Prompt File:** `/prompts/M3_S2_T10_Implement_Kernel_Interrupt_Handling.txt`
    *   **Initial Launch:** `/prompts/M3_S2_T10_Implement_Kernel_Interrupt_Handling.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S2_T10_Implement_Kernel_Interrupt_Handling.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

---

## Phase 2: User-Space Transition & Basic Component Framework

**Goal:** Enable the kernel to start initial user-space components. Implement basic user-space memory management, capability passing to user-space, and the initial Component Manager.

### Memory Management Subsystem (Module 3.4) - User-Space MM Support
*   **Task:** Implement `gRiTOS_MemoryObject` (VMO-like).
    *   **Prompt File:** `/prompts/M3_S4_T3_Implement_MemoryObject_VMO.txt`
    *   **Initial Launch:** `/prompts/M3_S4_T3_Implement_MemoryObject_VMO.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S4_T3_Implement_MemoryObject_VMO.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Implement `AddressSpace` object and methods for mapping/unmapping `gRiTOS_MemoryObject` instances.
    *   **Prompt File:** `/prompts/M3_S4_T4_Implement_AddressSpace_Map_Unmap.txt`
    *   **Initial Launch:** `/prompts/M3_S4_T4_Implement_AddressSpace_Map_Unmap.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S4_T4_Implement_AddressSpace_Map_Unmap.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Implement shared memory by mapping the same `gRiTOS_MemoryObject` into multiple `AddressSpace` objects.
    *   **Prompt File:** `/prompts/M3_S4_T5_Implement_Shared_Memory.txt`
    *   **Initial Launch:** `/prompts/M3_S4_T5_Implement_Shared_Memory.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S4_T5_Implement_Shared_Memory.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Implement memory fault handling: trap identification, delivery of exception IPC to user-space handlers.
    *   **Prompt File:** `/prompts/M3_S4_T6_Implement_Memory_Fault_Handling.txt`
    *   **Initial Launch:** `/prompts/M3_S4_T6_Implement_Memory_Fault_Handling.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S4_T6_Implement_Memory_Fault_Handling.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Ensure secure construction of exception IPC messages to avoid info leaks.
    *   **Prompt File:** `/prompts/M3_S4_T7_Secure_Exception_IPC_Construction.txt`
    *   **Initial Launch:** `/prompts/M3_S4_T7_Secure_Exception_IPC_Construction.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S4_T7_Secure_Exception_IPC_Construction.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

### Component Framework (User-Space) (Module 3.5)
*   **Task:** Define component manifest format.
    *   **Prompt File:** `/prompts/M3_S5_T1_Define_Component_Manifest_Format.txt`
    *   **Initial Launch:** `/prompts/M3_S5_T1_Define_Component_Manifest_Format.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S5_T1_Define_Component_Manifest_Format.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Design and implement a Component Manager service.
    *   **Prompt File:** `/prompts/M3_S5_T2_Implement_Component_Manager_Service.txt`
    *   **Initial Launch:** `/prompts/M3_S5_T2_Implement_Component_Manager_Service.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S5_T2_Implement_Component_Manager_Service.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Define component lifecycle.
    *   **Prompt File:** `/prompts/M3_S5_T3_Define_Component_Lifecycle.txt`
    *   **Initial Launch:** `/prompts/M3_S5_T3_Define_Component_Lifecycle.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S5_T3_Define_Component_Lifecycle.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Develop an Interface Definition Language (IDL).
    *   **Prompt File:** `/prompts/M3_S5_T4_Develop_IDL_For_Component_Interfaces.txt`
    *   **Initial Launch:** `/prompts/M3_S5_T4_Develop_IDL_For_Component_Interfaces.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S5_T4_Develop_IDL_For_Component_Interfaces.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Implement mechanisms for service discovery.
    *   **Prompt File:** `/prompts/M3_S5_T5_Implement_Service_Discovery.txt`
    *   **Initial Launch:** `/prompts/M3_S5_T5_Implement_Service_Discovery.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S5_T5_Implement_Service_Discovery.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

### User-Space System Services (Module 3.7) - Root Resource Manager
*   **Task:** Implement Root Resource Manager component logic.
    *   **Prompt File:** `/prompts/M3_S7_SS1_T1_Implement_Root_Resource_Manager_Logic.txt`
    *   **Initial Launch:** `/prompts/M3_S7_SS1_T1_Implement_Root_Resource_Manager_Logic.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S7_SS1_T1_Implement_Root_Resource_Manager_Logic.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Implement RRM IPC interfaces.
    *   **Prompt File:** `/prompts/M3_S7_SS1_T2_Implement_RRM_IPC_Interfaces.txt`
    *   **Initial Launch:** `/prompts/M3_S7_SS1_T2_Implement_RRM_IPC_Interfaces.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S7_SS1_T2_Implement_RRM_IPC_Interfaces.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

---

## Phase 3: Essential System Services & Drivers

**Goal:** Develop core user-space system services: device drivers for essential peripherals, time service, basic logging/monitoring.

### HAL & BSP (Module 3.6) - BSP and Devicetree
*   **Task:** Define BSP structure.
    *   **Prompt File:** `/prompts/M3_S6_T3_Define_BSP_Structure.txt`
    *   **Initial Launch:** `/prompts/M3_S6_T3_Define_BSP_Structure.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S6_T3_Define_BSP_Structure.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Develop Devicetree parsing and utilization mechanisms.
    *   **Prompt File:** `/prompts/M3_S6_T4_Develop_Devicetree_Parsing_Utilization.txt`
    *   **Initial Launch:** `/prompts/M3_S6_T4_Develop_Devicetree_Parsing_Utilization.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S6_T4_Develop_Devicetree_Parsing_Utilization.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Create a BSP for the initial reference hardware platform.
    *   **Prompt File:** `/prompts/M3_S6_T5_Create_BSP_For_Reference_Platform.txt`
    *   **Initial Launch:** `/prompts/M3_S6_T5_Create_BSP_For_Reference_Platform.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S6_T5_Create_BSP_For_Reference_Platform.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

### User-Space System Services (Module 3.7)
#### Device Drivers (Sub-Module 3.7.2)
*   **Task:** Develop a generic device driver model/framework.
    *   **Prompt File:** `/prompts/M3_S7_SS2_T1_Develop_Generic_Driver_Model.txt`
    *   **Initial Launch:** `/prompts/M3_S7_SS2_T1_Develop_Generic_Driver_Model.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S7_SS2_T1_Develop_Generic_Driver_Model.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Implement specific user-space drivers for core peripherals (UART, GPIO, SPI, I2C).
    *   **Prompt File:** `/prompts/M3_S7_SS2_T2_Implement_Core_Peripheral_Drivers.txt`
    *   **Initial Launch:** `/prompts/M3_S7_SS2_T2_Implement_Core_Peripheral_Drivers.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S7_SS2_T2_Implement_Core_Peripheral_Drivers.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

#### Time Services (Sub-Module 3.7.5)
*   **Task:** Implement a user-space time management service.
    *   **Prompt File:** `/prompts/M3_S7_SS5_T1_Implement_Time_Management_Service.txt`
    *   **Initial Launch:** `/prompts/M3_S7_SS5_T1_Implement_Time_Management_Service.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S7_SS5_T1_Implement_Time_Management_Service.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Implement Time Service IPC interfaces.
    *   **Prompt File:** `/prompts/M3_S7_SS5_T2_Implement_TimeService_IPC_Interfaces.txt`
    *   **Initial Launch:** `/prompts/M3_S7_SS5_T2_Implement_TimeService_IPC_Interfaces.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S7_SS5_T2_Implement_TimeService_IPC_Interfaces.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Integrate Time Service with hardware timers.
    *   **Prompt File:** `/prompts/M3_S7_SS5_T3_Integrate_TimeService_With_HW_Timers.txt`
    *   **Initial Launch:** `/prompts/M3_S7_SS5_T3_Integrate_TimeService_With_HW_Timers.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S7_SS5_T3_Integrate_TimeService_With_HW_Timers.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

#### System Monitoring & Logging Service (Sub-Module 3.7.6)
*   **Task:** Implement a user-space logging service and IPC.
    *   **Prompt File:** `/prompts/M3_S7_SS6_T1_Implement_Logging_Service_And_IPC.txt`
    *   **Initial Launch:** `/prompts/M3_S7_SS6_T1_Implement_Logging_Service_And_IPC.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S7_SS6_T1_Implement_Logging_Service_And_IPC.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Implement a user-space monitoring service and IPC.
    *   **Prompt File:** `/prompts/M3_S7_SS6_T2_Implement_Monitoring_Service_And_IPC.txt`
    *   **Initial Launch:** `/prompts/M3_S7_SS6_T2_Implement_Monitoring_Service_And_IPC.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S7_SS6_T2_Implement_Monitoring_Service_And_IPC.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

### Security Services (Module 3.8) - Basic HRoT
*   **Task:** Develop user-space driver and IPC interface for HRoT integration.
    *   **Prompt File:** `/prompts/M3_S8_T2_Develop_UserSpace_HRoT_Driver_And_IPC.txt`
    *   **Initial Launch:** `/prompts/M3_S8_T2_Develop_UserSpace_HRoT_Driver_And_IPC.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S8_T2_Develop_UserSpace_HRoT_Driver_And_IPC.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

### Functional Safety Services (Module 3.9) - Basic Error Detection/Handling
*   **Task:** Implement stack overflow detection support.
    *   **Prompt File:** `/prompts/M3_S9_T3_Implement_Stack_Overflow_Detection.txt`
    *   **Initial Launch:** `/prompts/M3_S9_T3_Implement_Stack_Overflow_Detection.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S9_T3_Implement_Stack_Overflow_Detection.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Design and implement user-space Health Monitoring components.
    *   **Prompt File:** `/prompts/M3_S9_T2_Implement_UserSpace_Health_Monitoring.txt`
    *   **Initial Launch:** `/prompts/M3_S9_T2_Implement_UserSpace_Health_Monitoring.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S9_T2_Implement_UserSpace_Health_Monitoring.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

---

## Phase 4: Advanced Services & Security Hardening

**Goal:** Implement more complex services like file systems and networking. Harden security features.

### User-Space System Services (Module 3.7)
#### File System Service (Sub-Module 3.7.3)
*   **Task:** Design and implement a user-space file system server.
    *   **Prompt File:** `/prompts/M3_S7_SS3_T1_Implement_FileSystem_Server.txt`
    *   **Initial Launch:** `/prompts/M3_S7_SS3_T1_Implement_FileSystem_Server.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S7_SS3_T1_Implement_FileSystem_Server.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Implement FS IPC interfaces.
    *   **Prompt File:** `/prompts/M3_S7_SS3_T2_Implement_FS_IPC_Interfaces.txt`
    *   **Initial Launch:** `/prompts/M3_S7_SS3_T2_Implement_FS_IPC_Interfaces.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S7_SS3_T2_Implement_FS_IPC_Interfaces.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Implement user-space Ethernet driver and storage controller drivers. (Storage part relevant here)
    *   **Prompt File:** `/prompts/M3_S7_SS2_T3_Implement_Ethernet_Storage_Drivers.txt`
    *   **Initial Launch:** `/prompts/M3_S7_SS2_T3_Implement_Ethernet_Storage_Drivers.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S7_SS2_T3_Implement_Ethernet_Storage_Drivers.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Integrate FS with storage device drivers.
    *   **Prompt File:** `/prompts/M3_S7_SS3_T3_Integrate_FS_With_Storage_Drivers.txt`
    *   **Initial Launch:** `/prompts/M3_S7_SS3_T3_Integrate_FS_With_Storage_Drivers.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S7_SS3_T3_Integrate_FS_With_Storage_Drivers.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

#### Networking Stack Service (Sub-Module 3.7.4)
*   **Task:** Design and implement a user-space networking stack component.
    *   **Prompt File:** `/prompts/M3_S7_SS4_T1_Implement_Networking_Stack_Component.txt`
    *   **Initial Launch:** `/prompts/M3_S7_SS4_T1_Implement_Networking_Stack_Component.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S7_SS4_T1_Implement_Networking_Stack_Component.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Implement NetStack IPC interfaces.
    *   **Prompt File:** `/prompts/M3_S7_SS4_T2_Implement_NetStack_IPC_Interfaces.txt`
    *   **Initial Launch:** `/prompts/M3_S7_SS4_T2_Implement_NetStack_IPC_Interfaces.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S7_SS4_T2_Implement_NetStack_IPC_Interfaces.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Integrate NetStack with user-space Ethernet driver.
    *   **Prompt File:** `/prompts/M3_S7_SS4_T3_Integrate_NetStack_With_Ethernet_Driver.txt`
    *   **Initial Launch:** `/prompts/M3_S7_SS4_T3_Integrate_NetStack_With_Ethernet_Driver.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S7_SS4_T3_Integrate_NetStack_With_Ethernet_Driver.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

### Security Services (Module 3.8)
*   **Task:** Implement Secure Over-the-Air (OTA) Update Manager component.
    *   **Prompt File:** `/prompts/M3_S8_T1_Implement_OTA_Update_Manager.txt`
    *   **Initial Launch:** `/prompts/M3_S8_T1_Implement_OTA_Update_Manager.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S8_T1_Implement_OTA_Update_Manager.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Implement anti-rollback protection for SBL and Kernel. (SBL part)
    *   **Prompt File:** `/prompts/M3_S1_T6_Implement_Anti_Rollback_Protection.txt`
    *   **Initial Launch:** `/prompts/M3_S1_T6_Implement_Anti_Rollback_Protection.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S1_T6_Implement_Anti_Rollback_Protection.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Integrate Hardware Root of Trust (HRoT) for secure key storage for bootloader verification. (SBL part)
    *   **Prompt File:** `/prompts/M3_S1_T7_Integrate_HRoT_For_Bootloader_Key_Storage.txt`
    *   **Initial Launch:** `/prompts/M3_S1_T7_Integrate_HRoT_For_Bootloader_Key_Storage.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S1_T7_Integrate_HRoT_For_Bootloader_Key_Storage.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Implement user-space Secure Storage service.
    *   **Prompt File:** `/prompts/M3_S8_T3_Implement_Secure_Storage_Service.txt`
    *   **Initial Launch:** `/prompts/M3_S8_T3_Implement_Secure_Storage_Service.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S8_T3_Implement_Secure_Storage_Service.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Develop and maintain threat models.
    *   **Prompt File:** `/prompts/M3_S8_T4_Develop_Maintain_Threat_Models.txt`
    *   **Initial Launch:** `/prompts/M3_S8_T4_Develop_Maintain_Threat_Models.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S8_T4_Develop_Maintain_Threat_Models.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

### Kernel Core (Module 3.2) & IPC Subsystem (Module 3.3) - Optimization & Hardening
*   **Task:** Optimize IPC pathways for low latency and bounded WCET.
    *   **Prompt File:** `/prompts/M3_S3_T5_Optimize_IPC_Pathways.txt`
    *   **Initial Launch:** `/prompts/M3_S3_T5_Optimize_IPC_Pathways.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S3_T5_Optimize_IPC_Pathways.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Ensure all kernel operations have bounded WCET. (Ongoing analysis & refinement)
    *   **Prompt File:** `/prompts/M3_S2_T13_Ensure_Bounded_WCET_For_Kernel_Ops.txt`
    *   **Initial Launch:** `/prompts/M3_S2_T13_Ensure_Bounded_WCET_For_Kernel_Ops.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S2_T13_Ensure_Bounded_WCET_For_Kernel_Ops.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

---

## Phase 5: Functional Safety Features & Mixed Criticality

**Goal:** Implement and test key functional safety mechanisms and robust Mixed-Criticality System support.

### Functional Safety Services (Module 3.9)
*   **Task:** Verify and Document Kernel Fault Containment & Isolation Mechanisms.
    *   **Prompt File:** `/prompts/M3_S9_T1_Verify_Kernel_Fault_Containment_Mechanisms.txt`
    *   **Initial Launch:** `/prompts/M3_S9_T1_Verify_Kernel_Fault_Containment_Mechanisms.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S9_T1_Verify_Kernel_Fault_Containment_Mechanisms.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Implement user-space watchdog management service.
    *   **Prompt File:** `/prompts/M3_S9_T4_Implement_Watchdog_Management_Service.txt`
    *   **Initial Launch:** `/prompts/M3_S9_T4_Implement_Watchdog_Management_Service.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S9_T4_Implement_Watchdog_Management_Service.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Develop fault recovery supervisor components.
    *   **Prompt File:** `/prompts/M3_S9_T5_Develop_Fault_Recovery_Supervisor.txt`
    *   **Initial Launch:** `/prompts/M3_S9_T5_Develop_Fault_Recovery_Supervisor.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S9_T5_Develop_Fault_Recovery_Supervisor.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Ensure support for Mixed-Criticality Systems (MCS) resource partitioning.
    *   **Prompt File:** `/prompts/M3_S9_T6_Ensure_MCS_Resource_Partitioning_Support.txt`
    *   **Initial Launch:** `/prompts/M3_S9_T6_Ensure_MCS_Resource_Partitioning_Support.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S9_T6_Ensure_MCS_Resource_Partitioning_Support.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Ensure reliability and data integrity for File System Service.
    *   **Prompt File:** `/prompts/M3_S7_SS3_T4_Ensure_FS_Reliability_Integrity.txt`
    *   **Initial Launch:** `/prompts/M3_S7_SS3_T4_Ensure_FS_Reliability_Integrity.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S7_SS3_T4_Ensure_FS_Reliability_Integrity.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

### Kernel Core (Module 3.2) & Other Core Systems
*   **Task:** Implement support for time partitioning (scheduler budgets) for MCS. (Kernel Core part)
    *   **Prompt File:** `/prompts/M3_S2_T6_Implement_Time_Partitioning_For_MCS.txt`
    *   **Initial Launch:** `/prompts/M3_S2_T6_Implement_Time_Partitioning_For_MCS.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S2_T6_Implement_Time_Partitioning_For_MCS.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Consider integration of a dedicated firewall component support. (Networking related)
    *   **Prompt File:** `/prompts/M3_S7_SS4_T4_Consider_Firewall_Component_Support.txt`
    *   **Initial Launch:** `/prompts/M3_S7_SS4_T4_Consider_Firewall_Component_Support.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S7_SS4_T4_Consider_Firewall_Component_Support.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Ensure secure and reliable audit trail for Logging Service.
    *   **Prompt File:** `/prompts/M3_S7_SS6_T3_Ensure_Secure_Reliable_Audit_Trail.txt`
    *   **Initial Launch:** `/prompts/M3_S7_SS6_T3_Ensure_Secure_Reliable_Audit_Trail.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S7_SS6_T3_Ensure_Secure_Reliable_Audit_Trail.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

---

## Phase 6: Broader Hardware Support & Ecosystem Development

*(Tasks from this phase would typically involve creating new BSPs, new HAL implementations for different architectures, and developing more comprehensive user-facing APIs and documentation. The existing prompts cover the foundational elements needed before this phase can be fully realized. Specific prompts for "Develop HAL for second architecture" or "Create POSIX API layer" would follow once the core system is stable.)*

---

## Phase 7: Certification & Long-Term Maintenance

*(This phase focuses on formal certification efforts and establishing LTS. Key inputs are the outputs of safety and security tasks from earlier phases.)*

### Kernel Core (Module 3.2) & Security Services (Module 3.8) & Functional Safety (Module 3.9) - Formal Efforts
*   **Task:** Formally verify key kernel properties. (Ongoing, with major effort here)
    *   **Prompt File:** `/prompts/M3_S2_T12_Formally_Verify_Key_Kernel_Properties.txt`
    *   **Initial Launch:** `/prompts/M3_S2_T12_Formally_Verify_Key_Kernel_Properties.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S2_T12_Formally_Verify_Key_Kernel_Properties.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Implement cryptographic algorithm compliance. (Final validation and documentation for certification)
    *   **Prompt File:** `/prompts/M3_S8_T5_Implement_Crypto_Algorithm_Compliance.txt`
    *   **Initial Launch:** `/prompts/M3_S8_T5_Implement_Crypto_Algorithm_Compliance.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S8_T5_Implement_Crypto_Algorithm_Compliance.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

*   **Task:** Perform WCET analysis for all safety-critical kernel and user-space paths. (Final analysis and reporting for certification)
    *   **Prompt File:** `/prompts/M3_S9_T7_Perform_WCET_Analysis_For_Critical_Paths.txt`
    *   **Initial Launch:** `/prompts/M3_S9_T7_Perform_WCET_Analysis_For_Critical_Paths.txt is the prompt`
    *   **Retry:** `The prompt for this task is: /prompts/M3_S9_T7_Perform_WCET_Analysis_For_Critical_Paths.txt (A previous Jules instance may have crashed. Your task is to determine what the previous instance completed and then continue and complete the task.)`

---
**Note:** This launch plan provides a general order. Some tasks within a phase can be parallelized. Always refer to the specific dependencies mentioned in each prompt.
The SBL A/B Update logic (`M3_S1_T4_Implement_SBL_AB_Update_Rollback.txt`) is listed in Phase 4 in the main dev plan, but its SBL components are foundational so included earlier for SBL completion, though full testing depends on the OTA manager from Phase 4.
This plan prioritizes getting a core, bootable system first, then layering services and advanced features.
