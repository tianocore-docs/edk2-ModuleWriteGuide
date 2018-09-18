<!--- @file
  5.4 Communication between UEFI Drivers

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

## 5.4 Communication between UEFI Drivers

This section describes the communication methods used by UEFI drivers.

### 5.4.1 Protocol

UEFI drivers can use protocol services to access protocol interfaces produced
by other modules.

The _UEFI Specification_ defines a group of boot services to handle protocols,
including:

Services to install protocols:

* `InstallProtocolInterface()`

* `ReInstallProtocolInterface()`

* `InstallMultipleProtocolInterfaces()`

Services to retrieve protocols:

* `LocateProtocol()`

* `HandleProtocol()`

* `OpenProtocol()`

Section 8.4.1 provides an example of usage.

### 5.4.2 Variable

UEFI drivers can read and write variables via the UEFI Runtime Services
`GetVariable()` and `SetVariable()`.

When using this service, the distinction between a UEFI driver and a Dxe driver
is that a Dxe driver must explicitly point out the dependency relationship for

`EFI_VARIABLE_ARCH_PROTOCOL` and `EFI_VARIABLE_WRITE_ARCH_PROTOCOL` in the
[depex] section of the Dxe driver's INF file`,` but a UEFI driver does not have
this section in the UEFI driver's INF file

**********
**Note:** For UEFI drivers, the EDK II build system will automatically append
the dependency information inherited from the **UefiEntryPointLib** into the
image section. This causes UEFI drivers to run after all Dxe architectural
protocols are installed.
**********

Section 8.4.2, provides an example of usage.
