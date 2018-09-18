<!--- @file
  8.7 Handler for EVT_SIGNAL_EXIT_BOOT_SERVICES

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

## 8.7 Handler for EVT_SIGNAL_EXIT_BOOT_SERVICES

Some DXE drivers need to place their controllers in a quiescent state or
perform other controller-specific actions at the time that an operating system
is about to take full control of the platform. In this case, the DXE driver
should create a signal type event that is notified when
`gBS->ExitBootServices()` is called by the EFI OS Loader.

**********
**Note:** The notification function for this event is not allowed to use the
Memory Allocation Services, or call any functions that use the Memory
Allocation Services, and should only call functions that are known not to use
Memory Allocation Services, because these services modify the current memory
map.
**********

The template code for the notification function and event registration is as
follows:

```c
VOID
EFIAPI
NotifyExitBootServices (
  IN EFI_EVENT  Event,
  IN VOID       *Context
  )
{
  //
  // Put driver-specific actions here.
  // No UEFI Memory Service may be used directly or indirectly.
  //
}
EFI_STATUS
EFIAPI
SampleDriverInitialize (
  IN EFI_HANDLE ImageHandle,
  IN EFI_SYSTEM_TABLE *SystemTable
)
{
  EFI_STATUS Status;
  EFI_EVENT ExitBootServicesEvent;
  //
  // TODO: Other initialization of entry point can be added here.
  //
  //
  // Here is just the sample of registration of
  // EVT_SIGNAL_EXIT_BOOT_SERVICES
  //
  Status = gBS->CreateEventEx (
                  EVT_NOTIFY_SIGNAL,
                  TPL_CALLBACK,
                  NotifyExitBootServices,
                  NULL, // Parameter Context can be passed here
                  &gEfiEventExitBootServicesGuid,
                  &ExitBootServicesEvent
                  );
  ASSERT_EFI_STATUS (Status);
}
```
