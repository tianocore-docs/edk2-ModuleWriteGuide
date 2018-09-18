<!--- @file
  3.3 Additional Steps for Library Instances

  Copyright (c) 2010-2018, Intel Corporation. All rights reserved.<BR>

  Redistribution and use in source (original document form) and 'compiled'
  forms (converted to PDF, epub, HTML and other formats) with or without
  modification, are permitted provided that the following conditions are met:

  1) Redistributions of source code (original document form) must retain the
     above copyright notice, this list of conditions and the following
     disclaimer as the first lines of this file unmodified.

  2) Redistributions in compiled form (transformed to other DTDs, converted to
     PDF, epub, HTML and other formats) must reproduce the above copyright
     notice, this list of conditions and the following disclaimer in the
     documentation and/or other materials provided with the distribution.

  THIS DOCUMENTATION IS PROVIDED BY TIANOCORE PROJECT "AS IS" AND ANY EXPRESS OR
  IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF
  MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO
  EVENT SHALL TIANOCORE PROJECT  BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL,
  SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO,
  PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS;
  OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY,
  WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR
  OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS DOCUMENTATION, EVEN IF
  ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

-->

## 3.3 Additional Steps for Library Instances

### 3.3.1 Define Produced Library Class

A library instance is always related to a single library class and implements
all interfaces defined in the library class. Therefore, the library class name
must be specified in the [Defines] section of the library instance INF file as
follows:

```ini
[Defines]
  LIBRARY_CLASS = UefiDriverEntryPoint|DXE_DRIVER DXE_RUNTIME_DRIVER
```

In above sample, UefiDriverEntryPoint is the library class name produced by the
library instance. In addition, following "`DXE_DRIVER DXE_RUNTIME_DRIVER`" are
the type of modules to the library instance supports.

### 3.3.2 Define a Library Constructor (Optional)

The library instance module can define a library constructor function that is
invoked by the entry point of each linked module. In a library constructor
function, some initialization work can be done before any library interface is
used:

```ini
[Defines]
  ......
  CONSTRUCTOR = HobLibConstructor
```

#### 3.3.2.1 Types of library constructor functions

There are three types of library constructor functions, according to the
different module type of library instance:

* Library instance in BASE module type:

```
EFI_STATUS
EFIAPI
BaseConstructor (
VOID
)
```

* Library instance in PEIM, PEI_CORE module type:

```
EFI_STATUS
EFIAPI
PeiServicesTablePointerLibConstructor (
IN EFI_PEI_FILE_HANDLE FileHandle,
IN CONST EFI_PEI_SERVICES **PeiServices
)
```

* Library instance in DXE_DRIVER, DXE_CORE, DXE_RUNTIME_DRIVER,
  UEFI_APPLICATION, UEFI_DRIVER module type:

```
EFI_STATUS
EFIAPI
DxeCorePerformanceLibConstructor (
IN EFI_HANDLE ImageHandle,
IN EFI_SYSTEM_TABLE *SystemTable
)
```

### 3.3.3 Define a Library Destructor (Optional)

The library instance module can define a library destructor function that is
invoked by `ExitDriver()` for `DXE_DRIVER`, `UEFI_DRIVER` etc. In a library
destructor function, some un-initialization works can be done.

The destructor function should be declared in an INF file explicitly, as
follows:

```ini
[Defines]
  ......
  DESTRUCTOR = HobLibDestructor
```

The prototype of the destructor function is the same as the constructor
function mentioned above.
