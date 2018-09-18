<!--- @file
  6.3 Prepare for Data PEI Foundation

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

## 6.3 Prepare for Data PEI Foundation

Upon completion, SEC will call the PEI Foundation entry point and transfer
control to it.

The PEI foundation Entry Point is defined as,

```c
typedef

VOID

EFIAPI

(*EFI_PEI_CORE_ENTRY_POINT)(

IN CONST EFI_SEC_PEI_HAND_OFF *SecCoreData,

IN CONST EFI_PEI_PPI_DESCRIPTOR *PpiList );
```
### 6.3.1 EFI_SEC_PEI_HAND_OFF * SecCoreData

SEC conveys the following handoff information to the PEI Foundation,  State of
the platform.

* Location and size of the Boot Firmware Volume (BFV).

* Location and size of the temporary RAM.

* Location and size of the stack.

The format is defined as the `EFI_SEC_PEI_HAND_OFF` structure.

An example of, the temporary memory layout from Nt32Pkg is shown below:

```
|-----------| <---- TemporaryRamBase + TemporaryRamSize 
|   Heap    | 
|           | 
|-----------| <---- StackBase / PeiTemporaryMemoryBase 
|           | 
|   Stack   | 
|-----------| <---- TemporaryRamBase
```

###### Figure 3 Temporary Memory Layout

The `EFI_SEC_PEI_HAND_OFF` data structure is populated as follows:

```c
SecCoreData->DataSize = sizeof (EFI_SEC_PEI_HAND_OFF);
SecCoreData->BootFirmwareVolumeBase = (VOID *)BootFirmwareVolumeBase;
SecCoreData->BootFirmwareVolumeSize = PcdWinNtFirmwareFdSize;
SecCoreData->TemporaryRamBase = (VOID *)(UINTN)LargestRegion;
SecCoreData->TemporaryRamSize = STACK_SIZE;
SecCoreData->StackBase = SecCoreData->TemporaryRamBase;
SecCoreData->StackSize = PeiStackSize;
SecCoreData->PeiTemporaryRamBase = (VOID *)((UINTN)
                                   SecCoreData->TemporaryRamBase + PeiStackSize);
SecCoreData->PeiTemporaryRamSize = STACK_SIZE - PeiStackSize;
```

### 6.3.2 EFI_PEI_PPI_DESCRIPTOR *PpiList

Besides the `EFI_SEC_PEI_HAND_OFF` data structure, SEC may transfer one
additional `PpiList` to the PEIM Foundation. For example, `PpiList` may include
`TEMPORARY_RAM_SUPPORT_PPI` and `SEC_PLATFORM_INFORMAITON_PPI`.

* `TEMPORARY_RAM_SUPPORT_PPI`

This service may be published by the SEC as part of the SEC-to-PEI handoff. If
so, it moves the Temporary RAM contents into Permanent RAM.

* `SEC_PLATFORM_INFORMAITON_PPI`

This service abstracts platform-specific information. It is necessary to convey
this information to the PEI Foundation so that it can locate the PEIM dispatch
order. In addition, it contains the maximum stack capabilities of this platform.
