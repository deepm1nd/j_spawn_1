# Device Drivers (`drivers`) Development Plan

## Overview

This document details the development plan for the Device Drivers (drivers) subsystem of GritOS. This module is located at `src/drivers`. It will contain drivers for various hardware devices.

## Responsibilities

*   Provide interfaces for interacting with hardware devices (e.g., serial ports, block devices).
*   Implement specific drivers for emulated hardware in QEMU (e.g., PL011 UART, VirtIO).

## Key C Components (Linux Inspiration - for translation to Rust)

*   `drivers/base/`: Core device model.
*   `drivers/char/tty_io.c`: TTY and serial driver infrastructure.
*   `drivers/block/`: Block device infrastructure.

## Rust Implementation Strategy

*   **Traits:** `Device`, `CharDevice`, `BlockDevice` for abstraction.
*   **Initial Drivers:** Serial driver (e.g., PL011 UART) for console I/O, Timer driver.
*   Focus on non-DMA drivers initially.

## Development Phases

1.  **Phase 1: Basic Device Model:** Define core `Device` traits.
2.  **Phase 2: Serial Driver:** Implement a serial driver.
3.  **Phase 3: Timer Driver:** Implement a system timer driver.

## Integration Points

*   **Architecture Specifics (`src/arch`):** For interrupt handling, I/O port access.
*   **Memory Management (`src/mm`):** For mapping device memory.
*   **Initialization (`src/init`):** For driver initialization.
*   **Kernel Core (`src/lib.rs`):** For basic driver setup.
