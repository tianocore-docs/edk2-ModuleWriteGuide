<!--- @file
  8.8 DXE Runtime Driver

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

## 8.8 DXE Runtime Driver

A DXE runtime driver executes in both boot services and runtime services
environments. This means the services that these modules produce are available
before and after `ExitBootServices()` is called, including the time that an
operating system is running. If `SetVirtualAddressMap()` is called, then
modules of this type are relocated according to virtual address map provided by
the operating system.

The DXE Foundation is considered a boot service component, so the DXE
Foundation is also released when `ExitBootServices()` is called. As a result,
runtime drivers may not use any of the UEFI Boot Services, DXE Services, or
services produced by boot service drivers after `ExitBootServices()` is called.

A DXE runtime driver defines `MODULE_TYPE` as `DXE_RUNTIME_DRIVER` in the INF
file. In addition, because the DXE runtime driver encounters
`SetVirtualAddressMap()` during its life cycle, it may need to register an
event handler for the event `EVT_SIGNAL_VIRTUAL_ADDRESS_CHANGE`.

### 8.8.1 INF File

Following is the example of `[Defines]` section for a driver named

`SampleDriverRuntimeDxe`. For DXE runtime driver, the `MODULE_TYPE` entry
should be `DXE_RUNTIME_DRIVER`

```ini
[Defines]
  INF_VERSION = 0x00010005
  BASE_NAME   = SampleDriverRuntimeDxe
  FILE_GUID   = XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
  MODULE_TYPE = DXE_RUNTIME_DRIVER
  ENTRY_POINT = SampleRuntimeDriverEntryPoint

[Depex] gSampleProtocolGuid
```

**********
**Note:** If module dependent on the new definitions and features in
**********

_EFI_BOOT_SERVICES/UEFI_RUNTIME_SERVICES-defined in UEFI specifications from
version 2.1 forward-the hex version need to be given in INF file [Defines]
section's UEFI_SPECFIICATION_VERSION field._

### 8.8.2 Handler for EVT_SIGNAL_VIRTUAL_ADDRESS_CHANGE

DXE runtime drivers may need to be notified when the operating system calls
`SetVirtualAddressMap()`. In this case, the DXE runtime driver must create a
signal type event that is notified when `SetVirtualAddressMap()` is called by
the operating system. This call allows the DXE runtime driver to convert
pointers from physical addresses to virtual addresses.

The notification function for this type of event is not allowed to use any of
the UEFI Boot Services, UEFI Console Services, or UEFI Protocol Services either
directly or indirectly because those services are no longer available when
`SetVirtualAddressMap()` is called.

Instead, this type of notification function typically uses `ConvertPointer()`
to convert pointers within data structures that are managed by the DXE runtime
driver from physical addresses to virtual addresses.

Template code for notification function and event registration is as follows:

```c
//
// This is the global pointer which needs converting
//
VOID *gGlobalPointer;
VOID
EFIAPI
NotifySetVirtualAddressMap (
  IN EFI_EVENT Event,
  IN VOID *Context
)
{
  gRT->ConvertPointer (
         EFI_OPTIONAL_POINTER,
         (VOID **)&gGlobalPointer
         );
}
EFI_STATUS
EFIAPI
SampleRuntimeDriverInitialize (
  IN EFI_HANDLE ImageHandle,
  IN EFI_SYSTEM_TABLE *SystemTable
)
{
  EFI_STATUS Status;
  EFI_EVENT SetVirtualAddressMapEvent;
  //
  // TODO: Other initialization of entry point can be added here.
  //
  //
  // Here is just the sample of registration of
  // EVT_SIGNAL_VIRTUAL_ADDRESS_CHANGE
  //
  Status = gBS->CreateEventEx (
                  EVT_NOTIFY_SIGNAL,
                  TPL_CALLBACK,
                  NotifySetVirtualAddressMap,
                  NULL, // Parameter Context can be passed here
                  &gEfiEventVirtualAddressChangeGuid,
                  &SetVirtualAddressMapEvent
                  );
  ASSERT_EFI_STATUS (Status);
}
```
