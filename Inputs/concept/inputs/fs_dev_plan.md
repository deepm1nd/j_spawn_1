# File Systems (`fs`) Development Plan

## Overview

This document details the development plan for the File Systems (fs) subsystem of GritOS. This module is located at `src/fs`. It will provide an abstraction layer for file operations and manage different file system types.

## Responsibilities

*   Virtual File System (VFS) layer providing a unified interface.
*   Inode and dentry (directory entry) management.
*   Support for initial RAM-based filesystem (initramfs/initrd).

## Key C Components (Linux Inspiration - for translation to Rust)

*   `fs/vfs_inode.c`, `fs/dcache.c`: Inode and dentry cache management.
*   `fs/super.c`: Superblock operations.
*   `fs/namei.c`: Pathname lookup.
*   `init/do_mounts.c`: Filesystem mounting.

## Rust Implementation Strategy

*   **Traits:** `FileSystem`, `Inode`, `DirectoryEntry` for abstraction.
*   **Structs:** `Vfs`, `MountPoint`; concrete implementation for `ramfs`.
*   Use `Rc<RefCell<T>>` or `Arc<Mutex<T>>` for shared VFS objects.

## Development Phases

1.  **Phase 1: VFS Core:** Design basic VFS traits and structures.
2.  **Phase 2: Initial Filesystem:** Implement a simple RAM-based filesystem (e.g., `ramfs` or `initramfs` loader).
3.  **Phase 3: Mounting:** Implement logic for mounting filesystems.

## Integration Points

*   **Memory Management (`src/mm`):** For page cache, memory-mapped files.
*   **Initialization (`src/init`):** For mounting the root filesystem.
*   **Device Drivers (`src/drivers`):** For block device interaction.
