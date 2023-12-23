<!--- @file
  5.2 Write the UEFI Driver entry point

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

## 5.2 Write the UEFI Driver entry point

The following table lists the most common protocols used in UEFI driver entry
point.

###### Table 9 Protocols Used to Separate the Loading and Starting/Stopping of Drivers

| `Protocol`                  | `Description`                                                                                                                                                                                                                                                                                                                                                                              |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Driver Binding Protocol     | Provides functions for starting and stopping the driver, as well as a function for determining if the driver can manage a particular controller. The UEFI Driver Model requires this protocol.                                                                                                                                                                                             |
| Component Name Protocol     | Provides functions for retrieving a human-readable name of a driver and the controllers that a driver is managing. While the UEFI Specification lists this protocol as optional, the _Developer's Interface Guide for 64-bit Intel Architecturebased Servers_ (hereafter referred to as "_DIG64 specification_" or "_DIG64_") lists this protocol as required for Itanium-based platforms. |
| Driver Diagnostics Protocol | Provides functions for executing diagnostic functions on the devices that a driver is managing. While the _UEFI Specification_ lists this protocol as optional, _DIG64_ lists this protocol as required for Itanium-based platforms. |

The UefiLib library class is provided to simplify the driver entry point of a
UEFI driver.

### 5.2.1 Example: APIs in UefiLib

The two APIs in UefiLib are shown below:

```c
EFI_STATUS
EfiLibInstallDriverBinding (
	IN CONST EFI_HANDLE ImageHandle ,
	IN CONST EFI_SYSTEM_TABLE * SystemTable ,
	IN EFI_DRIVER_BINDING_PROTOCOL * DriverBinding ,
	IN EFI_HANDLE DriverBindingHandle
);

EFI_STATUS
EfiLibInstallAllDriverProtocols2 (
	IN CONST EFI_HANDLE ImageHandle ,
	IN CONST EFI_SYSTEM_TABLE * SystemTable ,
	IN EFI_DRIVER_BINDING_PROTOCOL * DriverBinding ,
	IN EFI_HANDLE DriverBindingHandle ,
	IN CONST EFI_COMPONENT_NAME_PROTOCOL * ComponentName , OPTIONAL
	IN CONST EFI_COMPONENT_NAME2_PROTOCOL * ComponentName2 , OPTIONAL 
	IN CONST EFI_DRIVER_CONFIGURATION_PROTOCOL * DriverConfiguration ,OPTIONAL 
	IN CONST EFI_DRIVER_CONFIGURATION2_PROTOCOL * DriverConfiguration2 ,OPTIONAL
	IN CONST EFI_DRIVER_DIAGNOSTICS_PROTOCOL * DriverDiagnostics ,OPTIONAL
	IN CONST EFI_DRIVER_DIAGNOSTICS2_PROTOCOL * DriverDiagnostics2 OPTIONAL
);
```


### 5.2.2 Example: Entry point to the Abc driver

The following shows an example of the entry point to the Abc driver that
installs the Driver Binding Protocol `gAbcDriverBindingProtocol`, the Component
Name Protocol `gAbcComponentName`, the Component Name2 Protocol
`gAbcComponentName2,` the

Diagnostic Protocol `gAbcDriverDiagnostic`s and the Diagnostic2 Protocol

`gAbcDriverDiagnostics2` onto the Abc driver's image handle. This driver simply
returns the status from the UefiLib function
`EfiLibInstallAllDriverProtocols2()`

```c
EFI_DRIVER_BINDING_PROTOCOL gAbcDriverBinding = {
  AbcDriverBindingSupported,
  AbcDriverBindingStart,
  AbcDriverBindingStop,
  0xa,
  NULL,
  NULL
};
EFI_COMPONENT_NAME_PROTOCOL gAbcComponentName = {
  AbcComponentNameGetDriverName,
  AbcComponentNameGetControllerName,
  "eng"
};
EFI_COMPONENT_NAME2_PROTOCOL gAbcComponentName2 = {
  (EFI_COMPONENT_NAME2_GET_DRIVER_NAME) AbcComponentNameGetDriverName,
  (EFI_COMPONENT_NAME2_GET_CONTROLLER_NAME)
  AbcComponentNameGetControllerName,
  "en"
};
EFI_DRIVER_DIAGNOSTICS_PROTOCOL gAbcDriverDiagnostics = {
  AbcDriverDiagnosticsRunDiagnostics,
  "eng"
};
EFI_DRIVER_DIAGNOSTICS2_PROTOCOL gAbcDriverDiagnostics2 = {
  (EFI_DRIVER_DIAGNOSTICS2_RUN_DIAGNOSTICS) gAbcDriverDiagnosticsRunDiagnostics,
  "en"
};
EFI_STATUS
EFIAPI
AbcDriverEntryPoint (
  IN EFI_HANDLE        ImageHandle,
  IN EFI_SYSTEM_TABLE  *SystemTable
  )
{
  //
  // Initialize a simple EFI driver that follows the EFI Driver Model
  //
  return EfiLibInstallAllDriverProtocols (
           ImageHandle, // Driver's image handle
           SystemTable, // EFI System Table Pointer
           &gAbcDriverBinding, // Required parameters
           ImageHandle,
           // Handle for driver-related protocols &gAbcComponentName,
           // Component Name Procol. May be NULL.
           &gAbcComponentName2, // Component Name2 Procol. May be NULL.
           NULL, // Configuration Protocol. May be NULL.
           NULL // Configuration Protocol2 May be NULL.
           &gAbcDriverDiagnostics, // Diagnostics Protocol. May be NULL.
           &gAbcDriverDiagnostics2,// Diagnostics Protocol2 May be NULL.
           );
}
```
