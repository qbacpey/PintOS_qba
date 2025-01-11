# Pintos  

This is the course project for the Berkeley Operating Systems course (CS162). Students, working in teams, are required to extend the functionality of the following three modules using the Pintos operating system framework provided at the beginning of the course:  

1. **Process System** (Tag: `proj-userprog-completed`)  
2. **Thread System** (Branch: `proj-thread`)  
3. **File System** (Branch: `proj-filesys`)  

## Process System  

Enhancements to the user process module of Pintos include:  

- **Passing Command-Line Arguments** to user processes.  
- **Implementing the following process control system calls** (similar to their Linux counterparts):  
  - `exec`: Creates a new process to run the specified application (a hybrid of `fork` and `exec`).  
  - `wait`: Waits for the child process `pid` of the current process to complete.  
  - `halt`: Shuts down Pintos, used to familiarize students with system call execution.  
- **Implementing the following file operation system calls**:  
  - `create`, `remove`, `open`, `filesize`, `read`, `write`, `seek`, `tell`, `close`:  
    - `create`, `remove`: Create and delete files.  
    - `open`, `close`: Open a file and return a file descriptor; close a file corresponding to the given descriptor.  
    - `read`, `write`, `seek`, `tell`, `filesize`: Perform file operations.  

## Thread System  

Enhancements to the thread system module of Pintos include:  

- **Implementing a Strict Priority Scheduler** with priority values ranging from `0` to `63`:  
  - High-priority threads are always scheduled before low-priority threads, under any circumstance.  
  - Synchronization primitives (lock, semaphore, condition variable) prioritize granting resources to high-priority threads.  
  - **Priority Donation** is implemented to solve priority inversion caused by strict priority scheduling.  

- **Simplified `pthread` library** supporting the following system calls:  
  - `sys_pthread_create`: Create a user thread.  
  - `sys_pthread_exit`: Exit a user thread.  
  - `sys_pthread_join`: Suspend the current thread until the target thread completes execution.  
  - `lock_acquire`, `lock_release`, `sema_down`, `sema_up`: User-space synchronization primitives.  

- **Handling process termination events** in the following priority order (from low to high):  
  1. The main thread calls `pthread_exit`: The main thread waits for all other threads to exit naturally before terminating the process.  
  2. Any thread executes the `exit` system call: All threads in user mode must stop executing, and threads in kernel mode must exit immediately after leaving the kernel.  
  3. Any thread triggers an exception during execution: Same as `2`, but the process exit code must be `-1`.  

## File System  

Enhancements to the file system of Pintos include:  

- **Buffer Cache**:  
  - Supports caching up to 64 disk sectors.  
  - Implements an LRU-like scheduling policy.  
  - Functions as a Write-Back Cache.  
  - Ensures that only one thread can read or write a sector at a time, but different sectors of the same file can be accessed by multiple threads simultaneously.  

- **Extendable Files**:  
  - Supports random access to files.  
  - Implements a tree-structured indexing system:  
    - File data blocks can be scattered across the disk, without needing contiguous allocation.  
    - Supports at least doubly-indirect pointers, similar to Unix FFS.  
  - Properly handles sector allocation failures by rolling back to the pre-allocation state.  

- **Subdirectories**:  
  - Implements directory-related system calls such as `chdir`, `mkdir`, `readdir`, and `isdir`.  
  - Ensures that `open`, `close`, `exec`, `remove`, and `inumber` handle directory logic appropriately.  
  - Supports both relative and absolute paths.  
