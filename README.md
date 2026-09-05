> ⚠️ Warning<br>
> The library is currently being tested.

# CFS: cross-platform filesystem API in C89

A single header implementation of a cross-platform filesystem API inspired by
`std::filesystem`/`Boost.Filesystem` in `C89`.

## Usage:

```c++
// This should be done in a source file, not a header file.

// For Linux:
// #define _GNU_SOURCE 
//  Already defined by most compilers, required for some functions.
//  This should be defined as a compiler definition, not using a #define.
//  If defined in a .c file, it should be above all #includes.

// For Windows:
//  Be sure to use a toolchain that automatically defines _WIN32_WINNT
//  to enable symlinks. Or set it to 0xA00 to compile for >=Win10.

#define CFS_IMPLEMENTATION
#include <cfs/cfs.h>
```

## OS requirements

| Windows           | Linux |
|:------------------|:------|
| Windows **95+**\* | Any   |

\* Only compatibility with **Windows 2000+** are tested in a VM. Older Windows
versions are checked by modifying the `_WIN32_WINNT` value.

Some specific fixes are implemented for `FreeBSD` and `Darwin` but compatibility
won't be tested.

## Thread safety

If the error code `fs_error_code_t *` is not provided, the function uses internal
storage. When possible, the storage will be made **thread local**, but it is recommended
to either always provide the argument or treat the functions as **not** thread safe.

## Differences with std::filesystem

`std::filesystem` implementation across compilers is *extremely* inconsistent. This
library adopts the most **common** or **logical** way across various implementations,
or a **custom** one.

 - On `Windows`, paths above `MAX_PATH` *(260 chars)* length are supported.
 - Empty paths `""` are **not** transformed in `"."` and `NULL` paths are treated as 
   an error (**fs_cfs_error_invalid_argument**)
 - `fs_file_time_type` is based on the **UNIX** epoch on **all** OSs.
