# Portable Executable (PE) format

The standard file format for executables, DLLs (Dynamic Link Libraries), and other binary files on modern Windows systems. Like ELF, it’s a layered structure with its own intricacies.

**1. Why PE? A Replacement for Older Formats**

Before PE, Windows used the Portable Executable Format (PEF) which was later replaced by the current PE format. The PE format aimed to improve upon earlier formats and provide better support for dynamic linking, relocation, and security features.

**2. The PE File Structure: Layers of Information**

The PE file structure is also layered, though it differs significantly from ELF. Here's a breakdown:

*   **DOS Header (64 bytes):** This is the very first part of the file. It’s a legacy artifact from MS-DOS and contains minimal information, primarily used to ensure that the file is recognized as an executable.  It points to the PE Header.
*   **PE Header (20 bytes):** Contains crucial metadata about the entire PE file:
    *   **Magic Number:** Identifies the file as a PE file (`0x5A4D` or "MZ" in ASCII).
    *   **Major and Minor Version Numbers:** Indicate the PE format version.
    *   **Machine Type:** Specifies the target processor architecture (e.g., x86, AMD64).
    *   **NumberOfSections:** The number of sections in the Section Table.
    *   **PointerToOptionalHeader:**  A pointer to the Optional Header.

*   **Optional Header (Variable Size):** This is where most of the important information resides. It's divided into two parts: Standard Fields and Data Directories.
    *   **Standard Fields:**
        *   **EntryPointAddress:** The address in memory where execution should begin when the program is loaded.
        *   **ImageBase:**  The preferred base address for loading the image into memory.
        *   **Subsystem:** Specifies the subsystem type (e.g., Windows, Console).
        *   **DLLCharacteristics:** Flags indicating characteristics of the DLL or executable.
    *   **Data Directories:** Pointers to various data structures that provide additional information about the PE file. These directories include things like:
        *   Import Address Table (IAT): Used for resolving references to functions in imported libraries.
        *   Export Directory: Lists the functions exported by a DLL.
        *   Resource Directory: Contains resources like icons, strings, and bitmaps.

*   **Section Header Table:**  Describes each section within the file. Each entry contains:
    *   **Name:** A string identifying the section's purpose (e.g., `.text`, `.data`, `.rsrc`).
    *   **VirtualSize:** The size of the section in virtual address space.
    *   **PointerToRawData:**  The offset within the file where the section’s data begins.
    *   **Characteristics:** Flags indicating permissions and attributes (e.g., executable, readable, writable).

*   **Sections:** These are the actual data blocks that make up the program:
    *   **.text:** Contains executable code instructions.
    *   **.data:** Contains initialized global variables.
    *   **.bss:** Contains uninitialized global variables (zeroed out at startup).
    *   **.rsrc:**  Contains resources like icons, strings, and bitmaps.
    *   Other sections for debugging, relocation information, etc.

**3. Key Section Types Explained (PE)**

Similar to ELF, PE uses section types to categorize data:

*   **B_CODE:** Contains executable code. `.text` is a B_CODE section.
*   **B_DATA:** Contains initialized data.  `.data` is often a B_DATA section.
*   **B_NO_DATA:** Contains uninitialized data (zeroed out at startup). `.bss` is a B_NO_DATA section.
*   **B_RESOURCE:** Contains resources like icons, strings, and bitmaps.

**4. Dynamic Linking & DLLs**

PE's support for dynamic linking through DLLs is essential:

*   **DLLs (Dynamic Link Libraries):** Shared code modules that can be loaded at runtime.
*   **Import Table (IAT):**  A table within the PE file that lists functions imported from other DLLs. The loader resolves these imports at runtime.
*   **Export Table:** A table within a DLL that lists the functions it exports for use by other programs.

**5. Tools for Inspecting PE Files**

Here are some tools to examine PE files:

*   **`dumpbin` (Microsoft Visual Studio):**  A powerful tool for displaying detailed information about PE files.
    *   `dumpbin /HEADERS <file>`: Displays the PE header and optional header.
    *   `dumpbin /SECTION:<section_name> <file>`: Displays details of a specific section.
    *   `dumpbin /IMPORTS <file>`: Lists imported functions.
    *   `dumpbin /EXPORTS <file>`: Lists exported functions (for DLLs).

*   **`PE Explorer`:** A graphical tool for browsing PE file structure and contents.  (Requires installation)

*   **`CFF Explorer`:** Another popular graphical tool with more advanced features. (Requires installation)

*   **`file` (Unix-like tools):** Can identify the file type as a "PE executable" or "PE DLL".



**Summary Table of Key PE Components**

| Component | Description |
|---|---|
| **DOS Header** | Legacy artifact, points to PE Header |
| **PE Header** | Metadata about the entire file |
| **Optional Header** | Contains crucial information like entry point and subsystem |
| **Section Header Table** | Describes each section within the file |
| **Sections (.text, .data, .bss, .rsrc, etc.)** | Actual code and data blocks |
| **Dynamic Linking (DLLs)** | Mechanism for using shared libraries |

**Further Exploration:**

*   Microsoft's documentation on PE: [https://learn.microsoft.com/en-us/windows/win32/debug/pe-format](https://learn.microsoft.com/en-us/windows/win32/debug/pe-format)
*   PEiD (PE Identifier): A tool for identifying the compiler and packer used to create a PE file.

**Key Differences from ELF:**

*   **DOS Header:**  A legacy artifact unique to PE.
*   **Optional Header Structure:** The Optional Header in PE is more complex than the ELF header, with its Standard Fields and Data Directories.
*   **Section Names:** Section names are often different (e.g., `.rsrc` for resources instead of ELF's `.rodata`).
