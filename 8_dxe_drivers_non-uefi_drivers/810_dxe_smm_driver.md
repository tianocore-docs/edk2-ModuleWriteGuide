<!--- @file
  8.10 DXE SMM Driver

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

## 8.10 DXE SMM Driver

This module type is used by SMM Drivers that are loaded into SMRAM. As a
result, this module type is only available for IA-32 and x64 CPUs. These
modules are dispatched by SMM Foundation and are never destroyed. This means
the services that these modules produce are available after
`ExitBootServices()`.

The lifecycle of SMM drivers can be divided into two phases, which have
different constraints. `SMM Initialization:`

This is the phase of SMM Driver initialization that starts with the call to the
driver's entry point and ends with the return from the driver's entry point.

**SMM Runtime:**

This is the phase of SMM Driver initialization that starts after the return
from the driver's entry point.

### 8.10.1 INF File

Following is the example of `[Defines]` section for a driver named

SampleDriverDxeSmm. For a SMM driver, the `MODULE_TYPE` is `DXE_SMM_DRIVER`.

```ini
[Defines]
  INF_VERSION              = 0x00010005
  BASE_NAME                = SampleDriverDxeSmm
  FILE_GUID                = XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
  MODULE_TYPE              = DXE_SMM_DRIVER
  PI_SPECIFICATION_VERSION = 0x0001000A
  ENTRY_POINT              = SampleDxeSmmDriverEntryPoint

[Depex]
  gSampleProtocolGuid
```

**********
**Note:** PISPECIFICATION_VERSION should be definitely set to 0x0001000A for PI_
**********

_1.1 compliant SMM drivers._

### 8.10.2 Constraints

The SMM driver model has constraints similar to those of DXE Runtime Drivers.

Inside of SMM Runtime, the drivers may not be able to use core protocol
services.

There are SMST-based services, which the drivers can access, but the UEFI
System Table and other protocols installed during boot services may not
necessarily be available.

Inside of SMM Initialization, the full collection of UEFI Boot Services, UEFI
Runtime Services and SMST-based services are available.

#### 8.10.2.1 SMM Driver Initialization

An SMM Driver's initialization phase begins when the driver is loaded into
SMRAM, and its entry point is called. An SMM Driver's initialization phase ends
when the entry point returns.

During SMM Driver initialization, SMM Drivers have access to two sets of
protocols:

UEFI protocols and SMM protocols.

UEFI protocols are those installed and discovered using UEFI Boot Services.
UEFI protocols can be located and used by SMM drivers only during SMM
Initialization.

SMM protocols are those installed and discovered using the System Management
Services Table (SMST). SMM protocols can be discovered by SMM drivers during
initialization phase and SMM runtime phase.

SMM Drivers must not use the UEFI Boot Services `Exit()` and
`ExitBootServices()` during SMM Driver Initialization.

#### 8.10.2.2 SMM Driver Runtime

During SMM Driver runtime, SMM drivers only have access to SMST-based services.
In addition, depending on the platform architecture, memory areas outside of
SMRAM may not be accessible to SMM Drivers. Likewise, memory areas inside of
SMRAM may not be accessible to UEFI drivers.

These SMM Driver Runtime characteristics lead to several restrictions regarding
the usage of UEFI services:

* UEFI interfaces and services located during SMM Driver Initialization must
  not be called or referenced during SMM Driver Runtime. This includes the EFI
  System Table, the UEFI Boot Services, and the UEFI Runtime Services.

* Events created during SMM Driver Initialization must be closed before exiting
  the driver entry point.
