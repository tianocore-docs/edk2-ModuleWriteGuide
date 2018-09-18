<!--- @file
  7.6 Communicate with DXE Modules

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

## 7.6 Communicate with DXE Modules

There are three methods for PEIMs to communicate with each other: PPIs, HOBs
and PCDs.

### 7.6.1 HOB

PEIMs can pass some information to the DXE Foundation and DXE modules, such as
the information of a memory bank discovered in PEI phase, by using a Hand-Off
Block (HOB).

In EDK II, the Hob Library provides a set of interfaces to help to build Hobs,
such as `BuildGuidHob()`. For example:

```c
EFI_MEMINIT_CONFIG_DATA *ConfigData;
EFI_PEI_HOB_POINTERS Hob;
UINTN BufferSize;
BufferSize = sizeof (EFI_MEMINIT_CONFIG_DATA);
Hob.Raw = BuildGuidHob (
            &gEfiMemoryConfigDataGuid,
            BufferSize
            );
ASSERT (Hob.Raw);
ConfigData = (EFI_MEMINIT_CONFIG_DATA *) Hob.Raw;
CopyMem (
  ConfigData->SpdData,
  SpdData,
  sizeof (MEMINIT_SPD_DATA) * MAX_SOCKETS
  );
```

In EDK II, the Hob Library also provides a set of APIs to locate HOBs for PEIMs
and DXE drivers.

### 7.6.2 Variable

PEIMs can read variables previously assigned by DXE drivers. PEIMs cannot write
variables.

PEIMs can use ReadOnlyVariable2 PPI to obtain variables. Follow these steps:

1. Locate ReadOnlyVariable2 PPI.

2. Invoke `GetVariable()` with size is 0, to get variable's actual size.

3. Allocate memory for variable.

4. Invoke `GetVariable(),` again with actual size to get the variable.

The following is one example of how to obtain the variable.

```c
Status = PeiServicesLocatePpi (
           &gEfiPeiReadOnlyVariable2PpiGuid,
           0,
           NULL,
           (VOID **) &VariablePpi
           );
ASSERT_EFI_ERROR (Status);
Size = 0;
Status = VariablePpi->GetVariable (
                        VariablePpi,
                        VariableName,
                        (EFI_GUID *) VariableGuid,
                        NULL,
                        &Size,
                        NULL
                        );
if (Status == EFI_BUFFER_TOO_SMALL)
{
  Status = PeiServicesAllocatePool (Size, &Buffer);
  ASSERT_EFI_ERROR (Status);
  Status = VariablePpi->GetVariable (
                          VariablePpi,
                          (UINT16 *) VariableName,
                          (EFI_GUID *) VariableGuid,
                          NULL,
                          &Size,
                          Buffer
                          );
  ASSERT_EFI_ERROR (Status);
  *VariableSize = Size;
  *VariableData = Buffer;
}
```

### 7.6.3 PCD

PEIMs can communicate with DXE drivers through dynamic PCDs. As with variables,
PEIMs can get dynamic PCDs values that were previously set by DXE drivers. The
usage for obtaining PCDs is covered in Appendix A .
