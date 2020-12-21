<!--- @file
  4.2 Write UEFI Application Entry Point

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

## 4.2 Write UEFI Application Entry Point

Developers must focus on specifying the entry point of UEFI application in the
`[Defines]` section of INF file.

Its prototype is list below:

```
EFI_STATUS
EFIAPI
UefiMain (IN EFI_HANDLE ImageHandle, 
          IN EFI_SYSTEM_TABLE *SystemTable);
```

As can be seen, there are two parameters for UEFI application entry point,

`ImageHandle` and `SystemTable`. `ImageHandle` refers to the image handle of
the UEFI application. `SystemTable` is the pointer to the EFI System Table.

The following is a full UEFI_APPLICATION example located at
`$WORKSPACE\MdeModulePkg\Application\HelloWorld`. It shows how to print a "UEFI
Hello World!" string to console.

**********
**Note:** This application uses several pcds to demonstrate the usage of PCD.
Readers can obtain the default value of these pcds from the
**********

_$WORKSPACE\MdeModulePkg\MdeModulePkg.dec file._

```c
EFI_STATUS
EFIAPI
UefiMain (
  IN EFI_HANDLE        ImageHandle,
  IN EFI_SYSTEM_TABLE  *SystemTable
  )
{
  UINT32  Index;
  Index = 0;
  //
  // Three PCD type (FeatureFlag, UINT32 and String) are used as the
  // sample.
  //
  if (FeaturePcdGet (PcdHelloWorldPrintEnable)) {
    for (Index = 0;
         Index < PcdGet32 (PcdHelloWorldPrintTimes); Index ++) {
      //
      // Use UefiLib Print API to print string to UEFI console
      //
      Print ((CHAR16 *)PcdGetPtr (PcdHelloWorldPrintString));
    }
  }
  return EFI_SUCCESS;
}
```
