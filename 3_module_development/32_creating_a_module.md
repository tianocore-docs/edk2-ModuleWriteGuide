<!--- @file
  3.2 Creating a Module

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

## 3.2 Creating a Module

Driver and Library modules follows similar steps for creation:

1. Create or select the package in which the module will be located.

2. Create a directory for the module and put the INF file in the directory.

3. Add package dependencies to the INF file.

4. Add PPI,.Protocol, Guid, PCD,or Library Class dependencies to the INF file.

5. Add [depex] section to the INF file if this module depends on a PPI,
   Protocol, or Guid and the module type supports this section.

6. Create source file(s) and add relative path of source file(s) to the INF file

### 3.2.1 Location

A module is released and distributed within a package, so creating or selecting
the appropriate package for the new module is the first step.

#### 3.2.1.1 Choosing the Package

A Package in EDK II is used to contain similar definitions and modules. The
"similar" is recommended to be determined by following rules:

**Industry standard**

For example, MdePkg package contains the definitions from PIWG, UEFI, SMBIOS,
USB, PCI, etc, which are all industry standards.

**Similar technology**

For example, OptionRomPkg groups the definitions and modules related to Option
Rom technology.

**Business reason**

For example, IntelFrameworkPkg groups the definitions and modules for Intel
framework implementation.

**Platform**

For example, Nt32Pkg groups the definitions and modules required by Nt32
platform. In addition, a platform package also will provide a DSC and FDF file
for platform building.

At the beginning of developing a module, the module developers need to consider
the purpose and release process for the module to select the appropriate
package.

**********
**Note:** The packages in
[https://edk2.tianocore.org](https://edk2.tianocore.org/) _are basic core
packages._
**********

_Generally, a new module should not be created in them._

#### 3.2.1.2 Adding a Module Directory

A module directory should be added to the proper package with the following
recommendations:

* Put a library module to "`<Package Root Path>`/Library" directory.

* Put PROTOCOL, PPI, GUID, or Library Class definitions in "`<Package Root Path>`/Include/Protocol" or "`<Package Root Path>`/Include/Ppi" or "`<Package Root Path>`/Include/Guid" or "`<Package Root Path>`/Include/Library" directory respectively.

* Put a driver module in "`<Package Root Path>`" directory.

* Put an application module in "`<Package Root Path>`/Application" directory 
  Use recommend directory name for module as follows:

###### Table 2 Recommended name convention for module directory

| `Recommended directory name convention` | `Module Type`           |
| --------------------------------------- | ----------------------- |
| XxxPei                                  | PEIM, PEI_CORE          |
| XxxDxe                                  | DXE_DRIVER, UEFI_DRIVER |
| XxxRuntimeDxe                           | DXE_RUNTIME_DRIVER      |
| XxxxDxeSal                              | DXE_SAL_DRIVER          |
| XxxxLib                                 | Library Instance        |

### 3.2.2 Sample: Module Meta File - INF

Each module requires a module INF file in the root directory of the module.

A module is INF file (sometimes referred to as the module meta-file) includes
the following items:

* The module's basic information, such as name, GUID, module type, etc.

* The path to any packages the module is dependent on.

* The path to binary files or source files included in the module.

* A list of all interfaces required by the module, i.e. Protocol, Ppi, Guid.

* A list of all PCDs and Library classes required by the module.

* Others, such as dependency section depending on the module type.

#### 3.2.2.1 Example: Application Module INF

```ini
##
[Defines]
  INF_VERSION                = 0x00010005
  BASE_NAME                  = HelloWorld
  FILE_GUID                  = XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
  MODULE_TYPE                = UEFI_APPLICATION
  VERSION_STRING             = 1.0
  UEFI_SPECIFICATION_VERSION = 0x0002001E
  ENTRY_POINT                = UefiMain

#
# The following information is for reference only and not required by the
# build tools.
#
# VALID_ARCHITECTURES = IA32 X64 IPF EBC
#
##
[Sources.common]
  HelloWorld.c

##
[Packages]
  MdePkg/MdePkg.dec

##
[LibraryClasses]
  UefiBootServicesTableLib
  UefiApplicationEntryPoint
  UefiLib
  DebugLib
```

#### 3.2.2.2 Example: Library Module INF

Following is a sample INF file for PeiSevicesTablePointerLib.inf library
instance:

```ini
[Defines]
  INF_VERSION    = 0x00010005
  BASE_NAME      = PeiServicesTablePointerLib
  FILE_GUID      = XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
  MODULE_TYPE    = PEIM
  VERSION_STRING = 1.0
  LIBRARY_CLASS  = PeiServicesTablePointerLib|PEIM
  PEI_CORE SEC
  CONSTRUCTOR    = PeiServicesTablePointerLibConstructor

#
# VALID_ARCHITECTURES = IA32 X64 IPF EBC (EBC is for build
#only)
#
[Sources.common]
  PeiServicesTablePointer.c

[Packages]
  MdePkg/MdePkg.dec

[LibraryClasses]
  DebugLib
```

### 3.2.3 Adding a Package Dependency

The [Packages] section of the INF file describes all packages dependencies of
this module. The EDK II relative path of dependent package DEC file is
described in INF's [Packages] section as follows:

```ini
[Packages]
  MdePkg/MdePkg.dec
  IntelFrameworkPkg/IntelFrameworkPkg.dec
```

**********
**Note:** The path is from the root of the $workspace, not from the module
directory.
**********

A module should use the following rules for determining package dependency:

* The MdePkg package is required for all modules

* If using definitions from Intel framework specification, dependency on the
  IntelFrameworkPkg is required.

* Beyond the preceding rules, more package dependencies are introduced by
  referencing or using surface items, such as Protocol, Ppi, Guid, PCD, Library
  Class, etc. For example, if a module uses definitions or interfaces from the
  library class "HiiLib" that is defined in MdeModulePkg package, it would need
  to be dependent on the MdeModulePkg.

### 3.2.4 Adding Source Files

All module source code is described in the INF [sources] section, and is based
on following rules:

**********
**Note:** The UNIX-style path separator "/" should be used, not the
Windows*-style "\".
**********

 Different architecture sources are put in different source sections.

```ini
[Sources.common] # source in this section is suitable for all arch
  CheckSum.c ...

[Sources.Ia32] # source in this section is suitable for IA32 arch
  Ia32/Wbinvd.c | MSFT ...
  Ia32/WriteMm7.S | GCC ...

[Sources.X64] # source in this section is suitable for X64 arch X64/Thunk16.asm ...

[Sources.IPF] # source in this section is suitable for IPF arch Ipf/AsmCpuMisc.s ...

[Sources.EBC] # source in this section is suitable for EBC arch Synchronization.c ...
```

* Tool Tags are used describe the sources for different tool chains.

```ini
[Sources.Ia32]
  Ia32/Wbinvd.c | MSFT # source is built when MSFT tool is used ...
  Ia32/WriteMm7.S | GCC # source is built when GCC tool is used ...
  "$(CC)" -o ${dst} $(CC_FLAGS) $(INC) ${src}
```

* All files should be put under the module's main folder. Do not use ". /" 
  All C include files should also be listed in the sources section.

#### 3.2.4.1 Supported Tool Tag

The following tool tag name is supported by edk2.

###### Table 3 EDK II supported file extensions

| `Tool Tag` | `Description`               |
| ---------- | --------------------------- |
| MSFT       | Microsoft Family Tool Chain |
| INTEL      | INTEL Tool Chain            |
| GCC        | GCC Tool Chain              |

### 3.2.5 Add Library Class References

The library class abstracts some macro or structure definitions and function
declarations. The library instance, which is the implementation of a given
library class, can be different for different platform or for different phases
(SEC ,PEI, DXE) in one platform. Therefore, a module will be dependent on a
library class for platform or phase specific behavior.

The steps of using a library class in a module are:

1. Add a dependency for the package containing the library class in INF file

2. Add a dependency for the library class in the INF file

3. Include the library class header file in source code.

To include the library class header in C source code use the following syntax:

`#include <Library/OemHookStatusCodeLib.h>`

The header including path is relative to the package's public include path,
which is defined in the package (containing the library class, not necessarily
the module) DEC's [include] section.

**********
**Note:** Rememberr to use "/" for the path separator.
**********

### 3.2.6 Adding PCD References

MACRO and global variables are widely used to make modules customizable in
different architectures and different platforms. The EDK II introduces the PCD
concept to replace these methods. For example, a "FeatureFlag" type PCD is
similar to a project MACRO in that some feature or functionality will be
enabled if the PCD's value is TRUE, and vice versa.

A PCD entry is defined by the PCD's Token Space Guid C name, followed by a
period "." character and the PCD's C name. In one PCD's token space, each PCD's
C name is unique.

```c
For example, for PCD gEfiMdePkgTokenSpaceGuid.PcdDebugPrintErrorLevel, gEfiMdePkgTokenSpaceGuid is the token space name
and PcdDebugPrintErrorLevel is the PCD name, and gEfiMdePkgTokenSpaceGuid is mapped to a GUID defined in MdePkg :
gEfiMdePkgTokenSpaceGuid = { 0x914AEBE7, 0x4635, 0x459b, { 0xAA, 0x1C, 0x11, 0xE2, 0x19, 0xB0, 0x3A, 0x10 }}
```

#### 3.2.6.1 PCD Types

EDK II provides the following types of PCDs:

**Feature flag type PCD**

This PCD type replaces a switch MACRO to enable or disable a feature. This is a
Boolean value, and is either TRUE or FALSE.

**Fixed at build type PCD**

This PCD type replaces a macro that produced a customizable value, such as the
PCI Express base address. The value of this PCD type is determined at build
time and is stored in the code section of a module's PE image.

**Patchable in module type PCD**

This PCD type is very similar to the fixed at build PCD type, but the value is
stored in the data section, rather than the code section, of the module's PE
image.

**Dynamic type PCD**

This PCD type is different from the other PCD types listed. The value is
assigned by one module and is accessed by other modules in execution time. The
PEIM PcdPeim and the DXE Driver PcdDXE each maintain a PCD database that
contains all dynamic PCD information used by platform in their respective phase.

#### 3.2.6.2 Add the Package Dependency

When using a PCD in a module, package dependencies must be added to INF's
[Packages] section. Two packages are required: the package containing the PCD
being used, and the MdePkg. The MdePkg is required because the library class
"PcdLib" in MdePkg provides PCD accessing functions and macros.

#### 3.2.6.3 Adding PCDs to module's INF file

To reference a PCD entry, the token space guid name and PCD name must be added
into the INF's [PCD] section:

```ini
[PCD.common]
  gEfiMdePkgTokenSpaceGuid.PcdMaximumLinkedListLength gEfiMdePkgTokenSpaceGuid.PcdMaximumAsciiStringLength gEfiMdePkgTokenSpaceGuid.PcdMaximumUnicodeStringLength
```

We recommended using a general type "PCD" in the module's INF, so that it
allows platform integrators to choose any PCD type for different usage cases.
For example, in a desktop platform, memory length can be designated as a
"Dynamic" PCD, and its value is produced by the memory discovery driver.
However, in some special embedded systems, memory length is designed as a
"FixedAtBuild" type PCD, and its value is always fixed.

There are limitations for selecting PCD types:

 If a PCD value is used as constant value such as array's length, this PCD's
type should be "FixedPcd". For example:

`UINT8 MySampleArray[FixedPcdGet16(PcdArrayLength)] = {0};`

**********
**Note:** Avoid using "FixedPcd" in the library instance modules, because the
library instance can link to different modules, and the same PCD may have a
different value in different modules.
**********
**Note:** In a single module, avoid using two PCDs with same name but in
different token spaces.
**********

###### Table 4 INF PCD Section Name

| `PCD Type`                                      | `INF File Section Name` |
| ----------------------------------------------- | ----------------------- |
| General type that can be mapped in any PCD type | PCD                     |
| Feature Flag                                    | FeaturePcd              |
| Fixed At Build                                  | FixedPcd                |
| Patchable In Module                             | PatchPcd                |
| Dynamic                                         | PCD                     |

#### 3.2.6.4 Accessing a PCD value from C source code

To obtain or set a PCD's value from source code, the following steps should be
taken:

1. Add the dependency for PcdLib to the module INF file.

2. Add the dependency for MdePkg.

3. Add the include for `<Library/PcdLib.h>` in source code.

4. Use the PcdLib interface to access the PCD's value.

###### Table 5 PCD access functions

| `Function name`                 | `INF PCD Section Name`                    |
| ------------------------------- | ----------------------------------------- |
| PcdGetx()/PcdSetx()             | Common get/set function for all PCDs type |
| FeaturePcdGet()/FeaturePcdSet() | Get/set function for "FeaturePcd"         |
| FixedPcdGetx()                  | Get function for "FixedPcd"               |
| PatchPcdGetx()/PatchPcdSetx     | Get/Set function for "PatchPcd"           |

For example, the PcdGet32 macro is used to obtain the 32-bit value for all
types of PCDs:

```c
//
// Check driver debug mask value and global mask
//
if ((ErrorLevel &PcdGet32 (PcdDebugPrintErrorLevel)) == 0)
{
  return;
}
```

### 3.2.7 Referencing a Protocol, PPI, or GUID

A Protocol, PPI, or a Guid is a UEFI architecture interface item and abstract
firmware component's interface. This section introduces how to reference one of
these interfaces from a module.

#### 3.2.7.1 Adding Protocol, PPI, or GUID to INF file

The name of Protocol, PPI, or GUID must be added into the corresponding section
in the module INF file: [Protocol], [Ppi], and [Guid]. For example:

`[Protocol] gEfiSampleProtocol`

#### 3.2.7.2 Including the header file in source code

The Protocol, PPI, or GUID header file may define related structures. Use the
following procedure to find the header file path:

* The header file for a Ppi is in `<PackagePath>`\Include\Ppi.

* The header file for a Protocol is in `<PackagePath>`\Include\Protocol.

* The header file for a Guid is in `<PackagePath>`\Include\Guid.

Header files must be explicitly included in the module source code. For example:

```
#include <Protocol/DeviceIo.h>
#include <Ppi/Reset.h>
#include <Guid/GlobalVariable.h>
```

### 3.2.8 Adding a Dependency to a Module

The dependency expression gives the conditions for executing or dispatching a
driver's entry point. This helps determine the dispatch order for PEIM and DXE
modules.

An expression consists of one or more Protocol, PPI, or GUID with operators
such as "AND", "OR", "TRUE", "FALSE", "NOT" etc.

The _Platform Initialization Specification_ gives detailed syntax for PEIM's
dependency expression in the "Dependency Expressions" chapter, and details for
the DXE module's dependency expression in the "Dependency Expression Grammar"
chapter.

The expression should be put in the INF's depex section as follows:

```
[depex]

gEfiSampleGuid AND gEfiSamplePpiGuid
```