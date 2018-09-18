<!--- @file
  8.4 Communication between DXE Drivers

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

## 8.4 Communication between DXE Drivers

This section introduces communication channels between DXE drivers, including
protocol, variable, and PCD.

### 8.4.1 Protocol

This section will introduce how to produce and consume protocols. The _UEFI
Specification_ defines a group of boot services to handle protocols, including:

Services to install protocols

* `InstallProtocolInterface()`

* `ReInstallProtocolInterface()`

* `InstallMultipleProtocolInterfaces()`

Services to retrieve protocols

* `LocateProtocol()`

* `OpenProtocol()`

First, to make use of it, the module writer must declare the protocols for the
module in the INF and then write code to use the protocols.

The following example demonstrates how a DXE driver produces a protocol:

```c
//
// Handle for new protocol instance. Since it's NULL now, its value will
// be assigned by Boot Service InstallMultipleProtocolInterfaces()
//
EFI_HANDLE mNewHandle = NULL;
//
// The Sample Protocol instance produced by this driver
//
EFI_SAMPLE_PROTOCOL mSampleProtocol = {
  SampleProtocolApi
  //
  // More APIs can be added here
  //
};
//
// This is just a NULL function with no parameters. Necessary parameters
// and code can be added.
//
EFI_STATUS
EFIAPI
SampleProtocolApi (
   VOID
  )
{
  return EFI_SUCCESS;
}
EFI_STATUS
EFIAPI
SampleDriverInitialize (
  IN EFI_HANDLE        ImageHandle,
  IN EFI_SYSTEM_TABLE  *SystemTable
  )
{
  EFI_STATUS  Status;
  //
  // More initialization can be added here.
  //
  //
  // Install the Sample Protocol onto a new handle
  //
  Status = gBS->InstallMultipleProtocolInterfaces (
                  &mNewHandle,
                  &gEfiSampleProtocolGuid,
                  &mSampleProtocol,
                  NULL
                  );
  ASSERT_EFI_ERROR (Status);
  return EFI_SUCCESS;
}
```

The following example demonstrates how a DXE driver retrieves a protocol and
invokes the API:

```c
EFI_STATUS
SampleFunction (
   VOID
  )
{
  EFI_STATUS           Status;
  EFI_SAMPLE_PROTOCOL  *SampleProtocol;
  //
  // Locates the Sample Protocol from system.
  //
  Status = gBS->LocateProtocol (
                  &gEfiSampleProtocolGuid,
                  NULL,
                  (VOID **) &SampleProtocol
                  );
  if (EFI_ERROR (Status)) {
    return Status;
  }
  Status = SampleProtocol->SampleProtocolApi();
  return Status;
}
```

### 8.4.2 Variable

Variables are defined as key/value pairs that consist of identifying
information plus attributes (the key) and arbitrary data (the value). Variables
are intended for use as a means to store data that is passed between the EFI
environment implemented in the platform and EFI OS loaders and other
applications that run in the EFI environment.

A DXE driver can read and write variables via the UEFI Runtime Services
`GetVariable()` and `SetVariable()`.

**********
**Note:** These services are not available at the beginning of the DXE phase .
The PI Specification defines two architectural protocols to indicate the
readiness of read/write access to variables: **EFIVARIABLE_ARCH_PROTOCOL** and
**EFI_VARIABLE_WRITE_ARCH_PROTOCOL.**_
**********

DXE drivers that require read-only access or read/write access to volatile
environment variables must have `EFI_VARIABLE_ARCH_PROTOCOL` in their
dependency expressions.

DXE drivers that require write access to nonvolatile environment variables must
have the `EFI_VARIABLE_WRITE_ARCH_PROTOCOL` in their dependency expressions.

The full complement of environment variable services is not available until
both `EFI_VARIABLE_ARCH_PROTOCOL` and `EFI_VARIABLE_WRITE_ARCH_PROTOCOL` are
installed.

Sample code to read and write variables is as follows:

```
EFI_STATUS
ReadAndWriteVariable (
	IN CHAR16 *Name,
	IN EFI_GUID *VendorGuid,
){
	EFI_STATUS Status;
	UINTN BufferSize;
	VOID *Buffer;
	Buffer = NULL;
	//
	// Pass in a zero-size buffer to find the required buffer size.
	//
	BufferSize = 0;
	Status = gRT->GetVariable (
					Name,
					VendorGuid,
					NULL,
					&BufferSize,
					Buffer
					);
	//
	// If variable exists, the Status should be EFI_BUFFER_TOO_SMALL and
	// BufferSize has been updated.
	//
	if (Status != EFI_BUFFER_TOO_SMALL) {
	return Status;
	}
	//
	// Allocate the buffer according to updated BufferSize.
	//
	Buffer = AllocateZeroPool (BufferSize);
	ASSERT (Buffer != NULL);
	//
	// Read variable into the allocated buffer.
	//
	Status = gRT->GetVariable (
					Name,
					VendorGuid,
					NULL,
					&BufferSize,
					Buffer
					);
	if (EFI_ERROR (Status)) {
	BufferSize = 0;
	}
	//
	// TODO: Process of retrieved variable can be added here.
	//
	//
	// Now write back the processed variable.
	//
	Status = gRT->SetVariable (
					Name,
					VendorGuid,
					EFI_VARIABLE_BOOTSERVICE_ACCESS |
					EFI_VARIABLE_RUNTIME_ACCESS |
					EFI_VARIABLE_NON_VOLATILE,
					BufferSize,
					Buffer
					);
	ASSERT_EFI_ERROR (Status);
	return EFI_SUCCESS;
}
```

### 8.4.3 Dynamic PCD

EDK II provides dynamic PCDs as a high-level mechanism for communication
between modules. See Appendix A for details.
