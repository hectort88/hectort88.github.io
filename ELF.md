# Executable and Linkable Format - ELF

The Executable and Linkable Format (ELF) – the standard file format for executables, object code, shared libraries, and core dumps on most modern Linux systems.

**1. Why ELF? A Replacement for Older Formats**

Before ELF, Unix-like systems primarily used the COFF (Common Object File Format).  ELF was designed to address limitations of COFF and provide a more flexible and efficient format. Key advantages of ELF include:

*   **Flexibility:** Supports both 32-bit and 64-bit architectures.
*   **Dynamic Linking:** Efficiently handles shared libraries, reducing executable size and enabling code reuse.
*   **Security Features:** Includes mechanisms for security checks and integrity verification.
*   **Portability:** Designed to be portable across different processor architectures.

**2. The ELF File Structure: Layers of Information**

An ELF file is structured in a series of sections, which are grouped into segments. Think of it like an onion – layers upon layers of information. Let's break down the main components:

*   **ELF Header (52 bytes):** This is the first thing you encounter when reading an ELF file. It contains essential metadata about the entire file:
    *   **Magic Number:** Identifies the file as an ELF file (usually `0x4EF34B`).
    *   **Class:** Specifies whether it's a 32-bit or 64-bit format.
    *   **Data Encoding:**  Indicates the byte order (little-endian or big-endian).
    *   **ABI Version:** Application Binary Interface version.
    *   **Entry Point Address:** The address in memory where execution should begin when the program is loaded.
    *   **Program Header Offset/Size:** Points to the Program Header table.
    *   **Section Header Offset/Size:** Points to the Section Header table.

*   **Program Header Table (Optional):** This table describes how segments are mapped into memory at runtime. It's used for dynamic linking and loading shared libraries. Each entry in this table contains:
    *   **Type:**  Indicates the segment type (e.g., executable code, data, read-only data).
    *   **Address:** The virtual address where the segment should be loaded into memory.
    *   **Offset:** The file offset of the segment within the ELF file.
    *   **Size:** The size of the segment in bytes.
    *   **Flags:** Permissions (e.g., read, write, execute).

*   **Section Header Table:** This table describes each section within the file. Sections are logical groupings of code and data. Each entry contains:
    *   **Name:** A string identifying the section's purpose (e.g., `.text` for executable code, `.data` for initialized data, `.bss` for uninitialized data).
    *   **Type:**  Indicates the section type (e.g., PROGBITS, DATA, NOBITS).
    *   **Address:** The virtual address where the section should be loaded into memory.
    *   **Offset:** The file offset of the section within the ELF file.
    *   **Size:** The size of the section in bytes.
    *   **Flags:** Permissions and attributes (e.g., writeable, allocated).

*   **Sections:** These are the actual data blocks that make up the program:
    *   **.text:** Contains executable code instructions.
    *   **.data:** Contains initialized global variables.
    *   **.bss:** Contains uninitialized global variables (zeroed out at startup).
    *   **.rodata:** Read-only constant data.
    *   **.comment:**  Contains comments about the program.
    *   Other sections for debugging, relocation information, etc.

**3. Key Section Types Explained**

Let's look at some common section types:

*   **PROGBITS (Readable/Executable):** Contains code or data that will be loaded into memory and executed.  `.text` is a PROGBITS section.
*   **PROGBITS (Writable):** Similar to above, but also allows modification after loading. `.data` is often a writable PROGBITS section.
*   **NOBITS:** Contains data that should *not* be modified after loading. `.rodata` and `.bss` are NOBITS sections.  `.bss` sections don't actually contain any bytes in the ELF file; their space is reserved at load time and zeroed out.

**4. Dynamic Linking & Shared Libraries**

ELF’s support for dynamic linking is a crucial feature:

*   **Shared Libraries:** Code that can be shared between multiple programs, saving disk space and memory.
*   **Dynamic Linker (ld-linux.so):**  Responsible for resolving symbolic references to functions in shared libraries at runtime.
*   **Relocation Entries:** The ELF file contains relocation entries that tell the dynamic linker where to find addresses of symbols defined in shared libraries.

**5. Tools for Inspecting ELF Files**

Several tools can help you examine ELF files:

*   **`readelf`:**  The most common tool for displaying detailed information about an ELF file.
    *   `readelf -h <file>`: Displays the ELF header.
    *   `readelf -l <file>`: Displays the Program Header table.
    *   `readelf -S <file>`: Displays the Section Header table.
    *   `readelf -s <file>`: Lists all sections with their details.

*   **`objdump`:**  Can disassemble code and display other object file information.
    *   `objdump -f <file>`: Displays the ELF header information in a more human-readable format than `readelf -h`.
    *   `objdump -d <file>`: Disassembles the executable code.

*   **`file`:**  A simple command to determine the file type (e.g., "ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked...").



**Summary Table of Key ELF Components**

| Component | Description |
|---|---|
| **ELF Header** | Metadata about the entire file |
| **Program Header Table** | Describes how segments are loaded into memory |
| **Section Header Table** | Describes each section within the file |
| **Sections (.text, .data, .bss, etc.)** | Actual code and data blocks |
| **Dynamic Linking** | Mechanism for using shared libraries |

**Further Exploration:**

*   The official ELF specification is available from the IEEE: [https://ieeexplore.ieee.org/document/1250486](https://ieeexplore.ieee.org/document/1250486)
*   Many tutorials and articles online provide more detailed explanations of specific aspects of ELF.
