<!--- @file
  3.1 What is an EDK II module?

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

## 3.1 What is an EDK II module?

An EDK II module consists of source files or binary files and a module
definition file (INF file). An INF file describes a module's basic information
and interfaces such as consumed/produced library class/PCD/Protocol/Ppi/Guid.
(Please refer to the _EDK II Extended INF Specification_

A typical EDK II module is a firmware component that is built, put in an FFS
file and then put into a FV image. The component may be:

 A driver or application which is built to *.efi binary file and put into FFS
file as EFI_PE_SECTION:

<p align="center"> 
<img src="../media/image2.jpg" width="300">
</p>

###### Figure 2 Firmware Volume

* Raw data binary. For example, $(WORKSPACE)\MdeModulePkg\Logo\Logo.inf is a
  raw binary module which contains logo bitmap image.

* An option ROM driver that is put into a device's option ROM.

* A standalone UEFI driver

* A standalone UEFI application.

* A library instance that is built to a library object file (.lib) and
  statically linked to another module.

**********
**Note:** A module can be released in source code or in EFI binary format.
**********



### 3.1.1 Module Type

EDK II defines many module types. The module type is used to:

* Indicate the lifecycle for different types of modules. For example, PEIM is
  dispatched in PEI phase and DXE_DRIVER orUEFI_DRIVER is dispatched at DXE
  phase;

* Indicate the binary image generation for different types of modules. For
  example, a PEIM/DXE_DRIVER type module can have "depex" section in .efi
  binary image; a UEFI_DRIVER can have .ui or .ver section in .efi binary image;

* Indicate EntryPoint() or Constructor() API for different types of modules.

* Indicate the suitable library instance for different types of modules. A
  library instance will point out what module types are supported in INF file.

###### Table 1 EDK II Module Types

| `MODULE_TYPE`      | `Description`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SEC                | Modules of this type are designed to start execution at the reset vector of a CPU. They are responsible for preparing the platform for the PEI Phase. Since there are no standard services defined for SEC, modules of this type follow the same rules as modules of type Base and typically include some amount of CPU specific assembly code to establish temporary memory for a stack. Modules of this type may optionally produce services that are passed to the PEI Phase in HOBs and those services must be compliant with the PI specification. |
| PEI_CORE           | This module type is used by PEI Core implementations that are complaint with the PI specification.                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| PEIM               | This module type is used by PEIMs that are compliant with the PI specification.                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| DXE_CORE           | This module type is used by DXE Core implementations that are compliant with the PI specification.                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| DXE_DRIVER         | This module type is used by DXE Drivers that are complaint with the PI specification. These modules only execute in the                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|                    | boot services environment and are destroyed when ExitBootServices() is called.                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| DXE_RUNTIME_DRIVER | This module type is used by DXE Drivers that are complaint with the PI specification. These modules execute in both boot services and runtime services environments. This means the services that these modules produce are available after ExitBootServices() is called. If SetVirtualAddressMap() is called, then modules of this type are relocated according to virtual address map provided by the operating system.                                                                                                                               |
| DXE_SAL_DRIVER     | This module type is used by DXE Drivers that can be called in physical mode before SetVirtualAddressMap() is called and either physical mode or virtual mode after                                                                                                                                                                                                                                                                                                                                                                                      |
|                    | SetVirtualAddressMap() is called. This module type is only available to IPF CPUs. This means the services that these modules produce are available after ExitBootServices().                                                                                                                                                                                                                                                                                                                                                                            |
| DXE_SMM_DRIVER     | This module type is used by DXE Drivers that are loaded into                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|                    | SMRAM. As a result, this module type is only available for IA32 and x64 CPUs. These modules only execute in physical mode, and are never destroyed. This means the services that these modules produce are available after ExitBootServices().                                                                                                                                                                                                                                                                                                          |
| UEFI_DRIVER        | This module type is used by UEFI Drivers that are compliant with the _UEFI Specification_. These modules provide services in the boot services execution environment. UEFI Drivers that return EFI_SUCCESS are not unloaded from memory. UEFI Drivers that return an error are unloaded from memory.                                                                                                                                                                                                                                                    |
| UEFI_APPLICATION   | This module type is used by UEFI Applications that are compliant with the _UEFI Specification_. UEFI Applications are always unloaded when they exit.                                                                                                                                                                                                                                                                                                                                                                                                   |
