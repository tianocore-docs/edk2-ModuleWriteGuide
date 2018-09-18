<!--- @file
  8.1 Beginning with INF File

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

## 8.1 Beginning with INF File

Each DXE Driver requires an Extended INF file. For basic introduction of INF
file, please refer to Section 3.2.2.

The `[Defines]` section for DXE driver should be modeled after the following.

**********
**Note:** The MODULE_TYPE entry must be DXE_DRIVER.
**********

```[Defines]

INF_VERSION = 0x00010005 
BASE_NAME = SampleDriverDxe 
FILE_GUID = XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
MODULE_TYPE = DXE_DRIVER 
ENTRY_POINT = SampleDriverEntryPoint 
[Depex] gSampleProtocolGuid 
```
**********
**Note:** If DXE module dependent on the new definitions/features in
**********

_DXE_SERVICES_TABLE-defined in PI specifications from version 1.2 forward- the
hex version 0x0001000A needs to be given in INF file [Defines] section's
PI_SPECFIICATION_VERSION field._

**********
**Note:** If module dependent on the new definitions/features in
**********

_EFI_BOOT_SERVICES/UEFI_RUNTIME_SERVICES- defined in UEFI specifications from
version 2.1 forward-the hex version need to be given in INF file [Defines]
section's UEFI_SPECFIICATION_VERSION field._
