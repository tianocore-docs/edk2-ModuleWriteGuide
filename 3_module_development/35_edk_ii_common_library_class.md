<!--- @file
  3.5 EDK II Common Library Class

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

## 3.5 EDK II Common Library Class

| `Library Name`     | `Concept`                  | `Description`                                                                                                           |
| ------------------ | -------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| BaseLib            | Base Library               | Provides string functions, linked list functions, math functions, and CPU architecture specific functions.              |
| SynchronizationLib | Synchronization Library    | Provides synchronization functions                                                                                      |
| PrintLib           | Print Library              | Provides services to print a formatted string to a buffer. All combinations of Unicode and ASCII strings are supported. |
| BaseMemoryLib      | Base Memory Library        | Provides copy memory, fill memory, zero memory, and GUID functions.                                                     |
| MemoryAllocationL  | Memory Allocation Library  | Provides services to allocate and free memory buffers of various memory types and alignments.                           |
| ib                 |                            |                                                                                                                         |
| DebugLib           | Debug Library              | Provides ser vices to print debug and assert messages to a debug output device.                                         |
| PostCodeLib        | Post Code Library          | Provides services to send progress/error codes to a POST card.                                                          |
| StatusCodeLib      | Report Status Code Library | Provides services to log status code records                                                                            |
| IoLib              | I/O Library                | Provide services to access I/O Ports and MMIO registers.                                                                |
| PciExpressLib      | PCI Express Library        | Provides services to access PCI Configuration Space using the MMIO PCI Express window.                                  |
| PciLib             | PCI Library                | Provides services to access PCI Configuration Space.                                                                    |
| TimerLib           | Timer Library              | Provides calibrated delay and performance counter services.                                                             |
| PcdLib             | PCD Library                | Provides library services to get and set Platform Configuration Database entries.                                       |

The MdePkg provides many library classes for developing firmware components
based on the UEFI and PI specifications. These library classes are often used
in module development and detailed in the MdePkg documentation.

###### Table 6 Commonly use library classes

###### Table 7 Module Entry Point and Service Table Libraries

| `Concept`                            | `Description`                                                                                                            |
| ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------ |
| PEIM Entry Point Library             | Module entry point library for PEIM.                                                                                     |
| UEFI Driver Entry Point Library      | Module entry point library for UEFI drivers, DXE Drivers, DXE Runtime Drivers, and DXE SMM Drivers.                      |
| UEFI Application Entry Point Library | Module entry point library for UEFI Applications.                                                                        |
| PEI Services Table Pointer Library   | Provides a service to retrieve a pointer to the PEI Services Table.                                                      |
| UEFI Boot Services Table Library     | Provides a service to retrieve a pointer to the EFI Boot Services Table. Only available to DXE and UEFI module types.    |
| UEFI Runtime Services Table Library  | Provides a service to retrieve a pointer to the EFI Runtime Services Table. Only available to DXE and UEFI module types. |
| DXE Services Table Library           | Provides a service to retrieve a pointer to the DXE Services Table. Only available to DXE module types.                  |
