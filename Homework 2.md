# Homework 2

## Question 4: Building the xv6 kernel image

### Kernel build and memory layout

```mermaid
flowchart TB
    subgraph BUILD["Kernel build"]
        direction TB
        SRC["<strong><h4>Kernel Source Files</h4></strong>Assembly Files<br/>entry.S<br/><br/>C Files<br/>start.c<br/>main.c<br/>proc.c<br/>..."]
        O["<strong><h4>Kernel Object Files</h4></strong>entry.o<br/>start.o<br/>main.o<br/>proc.o<br/>..."]
        OBJS["<strong><h4>Makefile</h4></strong>Abbreviated OBJS list<br/><code>OBJS = #92;<br/>#160;#160;$K/entry.o #92;<br/>#160;#160;$K/start.o #92;<br/>#160;#160;$K/main.o #92;<br/>#160;#160;$K/proc.o #92;<br/>#160;#160;...</code>"]
        SCRIPT["<strong><h4>kernel/kernel.ld</h4></strong><ul><li>Linker script, not executable code</li><li>Sets the output architecture</li><li>Sets the entry point</li><li>Sets memory addresses</li><li>Sets section order</li><li>Sets alignment</li></ul>"]
        LINK["<strong><h4>RISC-V Linker</h4></strong><ul><li>Combines sections from the input object files</li><li>Resolves symbol references between object files</li><li></li></ul>"]

        SRC -->|Compile| O
        O -->|Input objects| LINK
        OBJS -.->|Selects input objects| LINK
        SCRIPT -.->|Sets layout| LINK
    end

    IMAGE["<strong><h4>Kernel Image</h4></strong>kernel/kernel (ELF)<br/><br/><table border='1' cellspacing='0' cellpadding='6' style='border-collapse:collapse;text-align:left'><tr><th>Starting address</th><th>Section</th><th>Content in memory</th></tr><tr><td>0x80000000</td><td>.text</td><td>_entry: kernel entry code<br/>Other kernel instructions<br/>start(), main(), usertrap(), kerneltrap()<br/>Trampoline code in an aligned 4 KiB region</td></tr><tr><td></td><td>.rodata</td><td>Read-only constants and string literals</td></tr><tr><td></td><td>.data</td><td>Initialized writable global/static variables</td></tr><tr><td></td><td>.bss</td><td>Zero-initialized global/static storage<br/>Occupies memory; no corresponding data bytes in the file</td></tr></table>"]

    LINK -->|Create Kernel Image| IMAGE

    classDef kernel fill:#eff6ff,stroke:#60a5fa,color:#0f172a;
    classDef script fill:#faf5ff,stroke:#c084fc,color:#0f172a;
    classDef image fill:#ffffff,stroke:#64748b,color:#0f172a;

    class SRC,O,OBJS,LINK kernel;
    class SCRIPT script;
    class IMAGE image;
```

The source, object, and Makefile nodes show four representative files to keep the diagram compact: `entry.S` (kernel entry), `start.c` (early startup), `main.c` (kernel initialization), and `proc.c` (process management and scheduling). All are in the `kernel/` directory. The `OBJS` excerpt is abbreviated; the actual Makefile links additional objects.

The Kernel Image table runs from lower addresses at the top to higher addresses at the bottom. Only the initial address is shown; later section addresses depend on section sizes and alignment. Alignment may add padding, and ELF metadata is omitted.

The linker combines contributions **by section**: an object's instructions contribute to `.text`, its constants to `.rodata`, and its variables to the appropriate data sections. Whole object files do not each occupy one consecutive block in the final image.

### User-program build

```mermaid
flowchart TB
    USRC["User sources<br/>sh.c, cat.c, ..."]
    UEXE["User executables<br/>user/_sh, user/_cat, ..."]
    MKFS["mkfs<br/>Packages programs listed in UPROGS"]
    FS["fs.img<br/>Filesystem image containing user programs"]

    USRC -->|Compile and link with user.ld and user libraries| UEXE
    UEXE --> MKFS
    MKFS --> FS

    classDef user fill:#f0fdf4,stroke:#4ade80,color:#0f172a;
    class USRC,UEXE,MKFS,FS user;
```

User programs such as `sh` and `cat` are built separately and placed in `fs.img`. They are not included in the kernel's `OBJS` list and are not linked into `kernel/kernel`.

Sources:

- [xv6 Makefile](https://raw.githubusercontent.com/mit-pdos/xv6-riscv/refs/heads/riscv/Makefile)
- [xv6 kernel linker script](https://raw.githubusercontent.com/mit-pdos/xv6-riscv/refs/heads/riscv/kernel/kernel.ld)
