Creating a shared library (also known as a dynamic library) with C and GCC, then linking it into your main program. 

**1. Creating the Shared Library:**

Let's say we have a file named `mylib.c` containing:

```c
// mylib.c
#include <stdio.h>

int add(int a, int b) {
    return a + b;
}

void greet(const char *name) {
    printf("Hello, %s!\n", name);
}
```

To create the shared library:

*   **GCC Command:** `gcc -shared -o libmylib.so mylib.c`

Let's break down this command:

*   `-shared`:  This option tells GCC to build a shared library.
*   `-o libmylib.so`: This specifies the output file name. Shared libraries typically have names starting with `lib` and ending in `.so` (for "Shared Object").  The convention is important for system-wide discovery of libraries.
*   `mylib.c`: The source code file to compile into a shared library.

**2. Using the Shared Library:**

Now, let's create a main program that uses functions from `libmylib.so`. Let's call it `main.c`:

```c
// main.c
#include <stdio.h>

int main() {
    int result = add(5, 3);
    printf("The sum is: %d\n", result);
    greet("Alice");
    return 0;
}
```

To compile and link against the shared library:

*   **GCC Command:** `gcc -o main main.c -L. -lmylib`

Let's break down this command:

*   `-o main`: Specifies the output executable file name (`main`).
*   `main.c`: The source code for your main program.
*   `-L.`: This tells the linker to search for libraries in the current directory (`.`).  You can specify other directories as well (e.g., `-L/usr/local/lib`).
*   `-lmylib`: This instructs the linker to link against a library named `libmylib.so`. The `-l` option tells the linker to search for a library with the prefix "lib" and suffix ".so".

**3. Running the Program:**

Before running, you need to tell the system where to find your shared library.  There are several ways to do this:

*   **Method 1 (Temporary - For Testing):**
    ```bash
    export LD_LIBRARY_PATH=".:$LD_LIBRARY_PATH"
    ./main
    ```
    This adds the current directory (`.`) to the `LD_LIBRARY_PATH` environment variable, which is used by the dynamic linker to find shared libraries at runtime.  The `$LD_LIBRARY_PATH` part appends your directory *after* any existing entries in that variable, ensuring it's searched first.

*   **Method 2 (Permanent - System-Wide):**
    You can add the library path to `/etc/ld.so.conf` or a file within `/etc/ld.so.conf.d/`.  After making changes, run `sudo ldconfig` to update the dynamic linker's cache. This is generally preferred for libraries that will be used by multiple programs.

*   **Method 3 (Symbolic Link):**
    Create a symbolic link in a standard library directory:
    ```bash
    sudo ln -s /path/to/your/library/libmylib.so /usr/lib/
    sudo ldconfig
    ```

**4. Example with an Installed Library:**

Let's say you have the `libpng` library installed on your system (usually found in `/usr/include/png.h` and `/usr/lib/libpng16.so`).  To use it:

*   **Compilation Command:**
    ```bash
    gcc -o myapp main.c -lpng16
    ```
    The `-lpng16` option tells the linker to link against `libpng16.so`. The linker will automatically search standard library directories (like `/usr/lib`, `/lib`) for this library.

*   **Important:** You might need to specify an include path if your header files are not in a standard location:
    ```bash
    gcc -o myapp main.c -I/path/to/png/include -lpng16
    ```
    The `-I` option specifies the directory containing the header files.

**Summary of Key Points:**

*   `-shared`:  Creates a shared library.
*   `-L<directory>`: Tells the linker to search for libraries in the specified directory.
*   `-l<library_name>`: Links against a library named `lib<library_name>.so`.
*   `LD_LIBRARY_PATH`: Environment variable used by the dynamic linker at runtime.  Use with caution, especially in production environments.
*   `/etc/ld.so.conf` and `sudo ldconfig`: For permanent system-wide changes to library paths.
