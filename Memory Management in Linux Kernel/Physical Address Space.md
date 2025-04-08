### 1. Physical Address Space

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
- 512M
- m 1G












