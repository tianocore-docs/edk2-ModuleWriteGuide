<!--- @file
  8.2 Write DXE Driver Entry Point

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

## 8.2 Write DXE Driver Entry Point

The `[Defines]` section of the INF file defines the entry point of the DXE
driver.

Unlike the UEFI driver entry point, which is only allowed to install protocol
instances onto its own image handle and may not touch any hardware, a DXE
driver entry point has no such restriction. It may install any protocol into
the system and perform necessary hardware and software initializations.

In the following example (from the WatchDogTimerDxe driver in the MdeModulePkg)
the DXE driver entry point installs its Architectural Protocol if the protocol
is not yet installed.

```c
EFI_STATUS
EFIAPI
WatchdogTimerDriverInitialize (
  IN EFI_HANDLE        ImageHandle,
  IN EFI_SYSTEM_TABLE  *SystemTable
  )
{
  EFI_STATUS  Status;
  //
  // Make sure the Watchdog Timer Architectural Protocol has not been
  // installed in the system yet.
  //
  ASSERT_PROTOCOL_ALREADY_INSTALLED (
    NULL,
    &gEfiWatchdogTimerArchProtocolGuid
    );
  //
  // Create the timer event to implement a simple watchdog timer
  //
  Status = gBS->CreateEvent (
                  EVT_TIMER | EVT_NOTIFY_SIGNAL,
                  TPL_NOTIFY,
                  WatchdogTimerDriverExpires,
                  NULL,
                  &mWatchdogTimerEvent
                  );
  ASSERT_EFI_ERROR (Status);
```

The two parameters for the DXE driver entry point are `ImageHandle` and
`SystemTable`. `ImageHandle` refers to the image handle of the DXE driver.

`SystemTable` points to the EFI System Table.
