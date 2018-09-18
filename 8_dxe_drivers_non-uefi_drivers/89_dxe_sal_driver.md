<!--- @file
  8.9 DXE SAL Driver

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

## 8.9 DXE SAL Driver

The module type of DXE SAL Driver is only available to the IPF architecture.
This module type is used by DXE Drivers that can be called in physical mode
before `SetVirtualAddressMap()` is called, and either physical mode or virtual
mode after `SetVirtualAddressMap()` is called. This means the services that
these modules produce are available after `ExitBootServices()`.

A DXE SAL driver defines `MODULE_TYPE` as `DXE_SAL_DRIVER` in the INF file. In
addition, a DXE SAL driver registers SAL Services for the system.

Because a DXE SAL Driver is available after `ExitBootServices()`, it may also
need to register an event handler for `EVT_SIGNAL_VIRTUAL_ADDRESS_CHANGE`.

### 8.9.1 INF File

Following is the example of a `[Defines]` section for a driver named

SampleDriverDxeSal. For DXE SAL driver, the `MODULE_TYPE` entry should be as
follows:

```ini
  DXE_SAL_DRIVER

[Defines]
  INF_VERSION = 0x00010005
  BASE_NAME   = SampleDriverDxeSal
  FILE_GUID   = XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
  MODULE_TYPE = DXE_SAL_DRIVER
  ENTRY_POINT = SampleDxeSalDriverEntryPoint

[Depex]
  gSampleProtocolGuid
```

### 8.9.2 Entry Point

The entry point of DXE SAL Driver must register the SAL services it produces.
The template code is as follows.

**********
**Note:** EDK II does not specify a detailed way for DXE SAL Drivers to produce
and register SAL services.
**********

```c
EFI_STATUS
EFIAPI
SampleDxeSalDriverEntryPoint (
  IN EFI_HANDLE        ImageHandle,
  IN EFI_SYSTEM_TABLE  *SystemTable
  )
{
  //
  // More initialization can be added here.
  //
  //
  // Event creation for EVT_SIGNAL_VIRTUAL_ADDRESS_CHANGE can be added
  // here.
  //
  //
  // Register SAL services
  //
  return EFI_SUCCESS;
}
```
