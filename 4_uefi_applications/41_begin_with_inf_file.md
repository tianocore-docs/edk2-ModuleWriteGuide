<!--- @file
  4.1 Begin with INF file

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

## 4.1 Begin with INF file

The following is an example of the INF file of an application named
SampleApplication. For UEFI Application, the `MODULE_TYPE` entry should be
`UEFI_APPLICATION.` The difference compared to Pei/Dxe/Uefi driver is that
UEFI_APPLICATON has no dependency relationship section.

```ini
[Defines]
  INF_VERSION                = 0x00010005
  BASE_NAME                  = SampleApplication
  FILE_GUID                  = XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
  MODULE_TYPE                = UEFI_APPLICATION
  ENTRY_POINT                = SampleApplicationEntryPoint
  UEFI_SPECIFICATION_VERSION = 0x0002001E

[Sources]
  Sample.c

[Packages]
  MdePkg/MdePkg.dec

[LibraryClasses]
  UefiApplicationEntryPoint
  DebugLib

[protocol]
  gSampleProtocolGuid

[Guids]
  gSampleGuid
```

**********
**Note:** If a module is dependent on the new definitions or features in
EFI_BOOT_SERVICES or UEFI_RUNTIME_SERVICES defined in UEFI specifications from
version 2.1 forward, the hex version needs to be given in INF file [Defines]
section's UEFI_SPECFIICATION_VERSION field.
**********
