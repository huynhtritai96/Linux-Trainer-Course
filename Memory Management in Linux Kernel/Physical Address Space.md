### 1. Physical Address Space
```
+------------------+  <- 0xFFFFFFFF (4GB)
|      32-bit      |
|  memory mapped   |
|     devices      |
|                  |
/\/\/\/\/\/\/\/\/\/\

/\/\/\/\/\/\/\/\/\/\
|                  |
|      Unused      |
|                  |
+------------------+  <- depends on amount of RAM
|                  |
|                  |
| Extended Memory  |
|                  |
|                  |
+------------------+  <- 0x00100000 (1MB)
|     BIOS ROM     |
+------------------+  <- 0x000F0000 (960KB)
|  16-bit devices, |
|  expansion ROMs  |
+------------------+  <- 0x000C0000 (768KB)
|   VGA Display    |
+------------------+  <- 0x000A0000 (640KB)
|                  |
|    Low Memory    |
|                  |
+------------------+  <- 0x00000000
```
1. **Definition:**
   - The physical address space refers to the entire range of memory addresses accessible by processors.

2. **32-bit Systems:**
   - A 32-bit system can have an address space of **2^32 = 4 GB**.

3. **Physical Address Space Usage:**
   - This space is used by:
     - **RAM**
     - **BIOS**
     - **APIC** (Advanced Programmable Interrupt Controller)
     - **PCI** (Peripheral Component Interconnect)
     - **Other Memory Mapped I/O Devices

4. **Address Range:**
   - The physical address space for a 32-bit system ranges from **0x00000000 to 0xFFFFFFFF (4GB)**.

5. **Memory-Mapped Devices:**
   - 32-bit systems can have memory-mapped devices within this range.
1. **Unused Space:** 
   - This space is dependent on the amount of installed RAM.

2. **Extended Memory:**
   - This is the area of memory used for additional storage.

#### Memory Map Breakdown (for 32-bit systems):
1. **32-bit memory mapped devices:** **0x FFFF FFFF** (Start From the Ending address)
2. **Unused Space:** **Unknown memory**
   - This space is dependent on the amount of installed RAM.

3. **Extended Memory:** **depends on amount of RAM**
   - This is the area of memory used for additional storage.

4. **Specific Address Ranges:**
   - **0x 0010 0000** - **BIOS ROM** (1MB)
   - **0x 000F 0000** - **BIOS ROM** (960KB)
   - **0x 000C 0000** - **16-bit Devices, Expansion ROMs** (768KB)
   - **0x 000A 0000** - **VGA Display** (640KB)
   - **0x 0000 0000** - **Low Memory** (Starting address)



### 2. Physical Address Space
- To show the memory map of your system
--> sudo cat /proc/iomem

### 3. Running 32-bit Buildroot image in QEMU
--> uname -a
output: 
```
Linux ubuntu 5.0.0-27-generic #28~18.04.1-Ubuntu SMP Thu Aug 22 03:00:32 UTC 2019 x86_64 x86_64 GNU/Linux
```
NOTE: x86_64 : machine have 64 bits 

- qemu-system-i386 -nographic -M pc -kernel output/image/s/bzImage -drive file=output/images/rootfs.ext2,if=virtio,format=raw -append "root=/dev/vda console=ttyS0,115200" -net nic,model=virtio -net user

### Breakdown:
- **qemu-system-i386**: Runs a 32-bit architecture virtual machine using QEMU.
- **-nographic**: Disables graphics and runs the machine in a text-based mode.
- **-M pc**: Specifies the machine type as a standard PC.
- **-kernel output/image/s/bzImage**: Specifies the kernel image to use.
- **-drive file=output/images/rootfs.ext2,if=virtio,format=raw**: Sets the virtual hard disk image.
- **-append "root=/dev/vda console=ttyS0,115200"**: Passes boot parameters to the kernel (root file system and console settings).
- **-net nic,model=virtio**: Configures a virtual network interface with virtio model.
- **-net user**: Sets up user networking for the virtual machine.

### 4. View/Change RAM Size in QEMU - /proc/meminfo, free -m

### Detailed Breakdown of the Commands and Outputs:

#### 1. **`free -m` Command Output:**

```bash
free -m
```
- **Purpose**: This command shows the system's memory usage in megabytes (MB).
- **Output Breakdown**:

```
            total        used      free      shared  buff/cache   available
Mem:        7950         1446      5485        17          1019       6229
Swap:       2047         0         2047
```

- **Mem**: Memory information.
  - **Total**: The total system memory available (7950 MB).
  - **Used**: Memory that is currently being used by processes (1446 MB).
  - **Free**: Memory that is not being used at all (5485 MB).
  - **Shared**: Memory shared between processes (17 MB).
  - **Buffer/Cache**: Memory used for buffers and cache (1019 MB).
  - **Available**: The amount of memory that can be used without swapping (6229 MB).

- **Swap**: Swap memory information.
  - **Total**: Total swap space available (2047 MB).
  - **Used**: Swap space currently being used (0 MB).
  - **Free**: Free swap space (2047 MB).

The **buffer/cache** value refers to the memory allocated for system buffers and cached data, which can be freed if needed by processes. The **available** memory value is crucial because it indicates how much memory is truly free for new processes.

#### 2. **`cat /proc/meminfo | less` Command Output:**

```bash
cat /proc/meminfo | less
```
- **Purpose**: This command provides detailed memory statistics from the `/proc/meminfo` file, which contains information about the system's memory usage.
- **Output Breakdown**:
```
MemTotal:       8141584 kB
MemFree:        5616288 kB
MemAvailable:   6378580 kB
Buffers:        97653 kB
Cached:         862096 kB
SwapCached:     150406 kB
Active:         357916 kB
Inactive:       600404 kB
```

Here are some key fields from the output:
- **MemTotal**: The total amount of physical memory (8 GB in this case).
- **MemFree**: Memory that is completely free and unused (5616288 kB).
- **MemAvailable**: Memory that is available for new processes, including both free and cached memory that can be freed up when needed (6378580 kB).
- **Buffers**: Memory used for file system buffers (97653 kB).
- **Cached**: Memory used for caching data (862096 kB).
- **SwapCached**: Memory used for swap that is still in the cache (150406 kB).
- **Active**: Memory actively being used by processes (357916 kB).
- **Inactive**: Memory that is not currently used but could be reclaimed if needed (600404 kB).

Other notable fields:
- **AnonPages**: Memory used by anonymous (non-file-backed) pages.
- **Mapped**: Memory used by mappings from files.
- **Shmem**: Shared memory used by processes.
- **Writeback**: Memory that is being written back to disk.
- **Slab**: Memory used by the kernel for data structures (like file system and network buffers).
- **SReclaimable**: Memory in the Slab that can be reclaimed.

These commands help monitor system memory in real time and provide insights into memory usage by different processes, caches, and buffers. It is essential for system administrators to keep track of these values to optimize system performance and diagnose memory-related issues.

For instance:
- If the **available** memory is low, it might indicate that the system could start swapping or running out of resources.
- The **swap** section helps track if the system is using swap memory, which is slower than physical RAM.
- The **Buffers/Caches** can be reclaimed by the system if necessary, making them an important part of available memory.

In the scenario shown in the images:
- The system has **1446 MB** of used memory out of **7950 MB** total.
- **5485 MB** of memory is free and available for use.
- **Swap space** is not being used, which means that all memory demands are currently being met by RAM without relying on swap.

#### Alocate memory for RAM 
--> qemu-system-i386 -nographic -M pc -kernel output/image/s/bzImage -drive file=output/images/rootfs.ext2,if=virtio,format=raw -append "root=/dev/vda console=ttyS0,115200" -net nic,model=virtio -net user -m 512M
```
- 512M
- m 1G
```

### 5. Virtual Address Space for 32-bit processors
```
      .------------------------. 0xFFFFFFFF 
      |                        | (4 GB) 
      |    Kernel addresses    | 
      |                        | 
      |                        | 
      .------------------------.CONFIG_PAGE_OFFSET 
      |                        |(x86: 0xC0000000, ARM: 0x80000000) 
      |                        | 
      |                        | 
      |  User space addresses  | 
      |                        | 
      |                        | 
      |                        | 
      |                        | 
      '------------------------' 00000000
```
1. **Virtual Memory**:
   - In Linux, every memory address is **virtual**. They do not point directly to addresses in physical RAM. Instead, when a memory location is accessed, a **translation mechanism** is performed to match the corresponding physical memory.

2. **Size of Virtual Address Space**:
   - The virtual address space is **4 GB** on 32-bit systems, even on systems with less than 4 GB of physical memory.

3. **Division of Virtual Address Space**:
   - The virtual address space is divided into two main parts:
     - **User Space**: This is the area allocated for applications.
     - **Kernel Space**: This is the area allocated for the kernel.

4. **3G/1G Split**:
   - The split between **user space** and **kernel space** is determined by a kernel configuration parameter called **PAGE_OFFSET**.
   - By default, this split is set as **3GB for user space** and **1GB for kernel space**.

5. **CONFIG_PAGE_OFFSET**:
   - The kernel configuration for `PAGE_OFFSET` is typically defined in the kernel source (e.g., for x86, it's set at `0x C000 0000`, for ARM, it's set at `0x 8000 0000`).

### 6.Why Kernel Shares its Address Space for All Processes

1. **User Space vs Kernel Space**:
   - **User address space** is allocated per process, meaning each process has its own space (like a sandbox, separated from other processes).
   - **Kernel address space** is the same for all processes, as there is only **one kernel** that handles all processes.

2. **Reason for Shared Kernel Space**:
   - **System Calls**: Every process uses system calls, which involve the kernel. If the kernel had separate memory spaces for each process, there would be an overhead of constantly switching between different memory spaces for each entry and exit from the kernel.
   - **Efficiency**: By mapping the kernel's memory into each process's virtual address space, the system avoids the cost of switching memory spaces every time a system call is made.

3. **Global Variables in Kernel**:
   - Since the kernel space is shared by all processes, any global variable in the kernel is accessible and can be modified by all processes. This enables processes to access and update shared kernel resources efficiently.



### 7. 64-bit Memory Map

1. **System Calls and Kernel Interaction**:
   - Every process uses system calls, which involve the kernel. Mapping the kernel’s virtual memory address into each process’s virtual address space helps avoid the overhead of switching memory address spaces on each entry to and exit from the kernel.

2. **64-bit Memory Map**:
   - The memory map is divided into **user-space** and **kernel-space**.
     - **User-space virtual memory**: Located in the range `0x0000000000000000` to `0x00007fffffffffff` (128 TB).
     - **Non-canonical**: In the range `0x0000800000000000` to `0xffff7fffffffffff` (~16M TB).
     - **Kernel-space virtual memory**: Located in the range `0xffff800000000000` to `0xffffffffffffffff` (128 TB).
```
===========================================================================================
    Start addr    |   Offset   |     End addr     |  Size   | VM area description
===========================================================================================
                  |            |                  |         |
 0000000000000000 |    0       | 00007fffffffffff |  128 TB | user-space virtual memory
__________________|____________|__________________|_________|______________________________
                  |            |                  |         |
 0000800000000000 | +128    TB | ffff7fffffffffff | ~16M TB | non-canonical
__________________|____________|__________________|_________|______________________________
                  |            |                  |         |
 ffff800000000000 | -128    TB | ffffffffffffffff |  128 TB | kernel-space virtual memory
__________________|____________|__________________|_________|______________________________
```
Documentation/x86/x86_64/mm.txt
  
### 8. Converting Virtual address to physical address and vice versa
Kinds of Memory
===============

Kernel and user space work with virtual addresses .

These virtual addresses are mapped to physical addresses by memory management hardware (MMU)

Header File: #include <asm/io.h>

phys_addr = virt_to_phys(virt_addr);
virt_addr = phys_to_virt(phys_addr);

``` hello.c
#include <linux/kernel.h>
#include <linux/module.h>
#include <asm/io.h>

MODULE_LICENSE("GPL");
static int __init test_hello_init(void)
{
    int i = 10;
    void *virtual_address = (void *)&i;
    phys_addr_t physical_address = virt_to_phys(virtual_address);

    pr_info("Virtual Address(hashed) of i is %p\n", virtual_address);
    pr_info("Virtual Address(unmodified) of i is %px\n", virtual_address);
    pr_info("Physical Address of i is %pa\n", &physical_address);
    pr_info("Virtual address of i is %px\n", phys_to_virt(physical_address));
    return -1;
}

static void __exit test_hello_exit(void)
{
}

module_init(test_hello_init);
module_exit(test_hello_exit);

Output:
Virtual Address (hashed) of i is 00000000cc9C3896
Virtual Address (unmodified) of i is fffff90344b93c54 --------
Physical Address of i is 0x0000203bc4b93c54
Virtual address of i is ffffb90344b93c54 ---------


Makefile
obj-m := hello.o

all:
	make -C /lib/modules/`uname -r`/build M=$(PWD) modules
clean:
	make -C /lib/modules/$(shell uname -r)/build M=$(PWD) clean

Document: /linux-5.2.8/Documentation/core-api/printk-format.rst


```

dmesg -w   
jobs    
kill %1   
kill %2   
mod    

#### Introduction:
In Linux kernel programming, when debugging or logging information using the `printk` function, it's important to use the correct format specifiers for various types of variables. This ensures that the output is properly formatted and readable.

#### Authors:
- Randy Dunlap <rdunlap@infradead.org>
- Andrew Murray <amurray@mpc-data.co.uk>

---

#### 1. **Integer Types and Corresponding Format Specifiers**:

Each variable type has its corresponding format specifier for `printk`. Below are the commonly used format specifiers for integer types:

| **Type**              | **Printk Format Specifier**     |
|-----------------------|---------------------------------|
| **char**              | `%hhd` or `%hhx`                |
| **unsigned char**     | `%hhu` or `%hhx`                |
| **short int**         | `%hd` or `%hx`                  |
| **unsigned short int**| `%hu` or `%hx`                  |
| **int**               | `%d` or `%x`                    |
| **unsigned int**      | `%u` or `%X`                    |
| **long**              | `%ld` or `%lx`                  |
| **unsigned long**     | `%lu` or `%lx`                  |
| **long long**         | `%lld` or `%llx`                |
| **unsigned long long**| `%llu` or `%llx`                |

These format specifiers are crucial for correctly printing the values of variables, particularly when debugging within the Linux kernel.

---

#### 2. **Pointer Types**:
Pointers can also be printed with specific format specifiers. The most common format for printing pointer values is `%p`. By default, `%p` hashes the address before printing to protect kernel memory information. If you need the actual address, you can use `%px`.

##### Extended Pointer Specifiers:
- `%p`: Prints the raw pointer, with the address hashed (to avoid leaking information about the kernel layout).
- `%px`: Prints the raw pointer without hashing, showing the actual address.

##### Error Messages for Invalid Pointers:
- **(null)**: Data on plain NULL address.
- **(efault)**: Data on invalid address.
- **(einval)**: Invalid data on a valid address.

These specifiers help prevent leaking information about the kernel memory layout while providing the necessary information for debugging.

---

#### 3. **Plain Pointers**:
On 64-bit machines, pointers are hashed by default to prevent leaking kernel memory layout. The `%p` specifier hashes the pointer address before printing. If you really need the actual address, you can use `%px`.

Example of printing pointers:
- Plain pointer: `%p` will display a hashed address, like `000000000000abcd`.
- Actual pointer: `%px` will print the unmodified address.

---











