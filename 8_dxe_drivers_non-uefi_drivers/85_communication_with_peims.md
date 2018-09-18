<!--- @file
  8.5 Communication with PEIMs

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

## 8.5 Communication with PEIMs

This section introduces communication channels between DXE driver and PEIM,
including HOB, variable, and PCD.

### 8.5.1 HOB

A HOB is a one-way channel to pass data from PEI to DXE. The HOB list is
provided during the PEI phase, and must be treated as a read-only data
structure in the DXE phase. It conveys the state of the system at the time the
DXE Foundation is started. The DXE drivers must not modify the contents of the
HOB list.

HobLib provides a set of APIs to build and parse a HOB list. Since DXE drivers
only read the HOB list, module writers of DXE drivers can focus on the APIs to
parse HOB list.

Several typical usage types are shown in examples below:

#### 8.5.1.1 Traversing all HOBs in the HOB list

```c
EFI_HOB_GENERIC_HEADER *Hob;
UINT16 HobType;
UINT16 HobLength;
for (Hob = GetHobList();
     !END_OF_HOB_LIST (Hob); Hob = GET_NEXT_HOB (Hob))
{
  HobType = GET_HOB_TYPE (Hob);
  HobLength = GET_HOB_LENGTH (Hob);
  //
  // Further operation on the HOB can be added
  //
}
```

#### 8.5.1.2 Retrieving only the first HOB of a specific type in the HOB list

```c
(CPU HOB type example)
EFI_HOB_CPU *CpuHob;
CpuHob = GetFirstHob (EFI_HOB_TYPE_CPU);
if (CpuHob != NULL)   //
{
  // Operation on the HOB can be added here.
  //
}
```

#### 8.5.1.3 Traversing specific types of HOBs in the HOB list (CPU HOB type example)

```c
EFI_HOB_CPU *Hob;
Hob = GetHobList ();
while ((Hob = GetNextHob (EFI_HOB_TYPE_CPU, Hob)) != NULL)   //
{
  // Operation on the HOB can be added here.
  //
  // At the end of loop, GET_NEXT_HOB must be added here.
  // GetNextHob (
       HobType, HobStart) does not skip the HOB passed by
       // parameter HobStart. It returns HobStart back if HobStart itself
       // meets the requirement. So it is required to use GET_NEXT_HOB() to
       // skip current HOB. Otherwise, it would be in dead loop.
       //
       Hob = GET_NEXT_HOB (Hob
       );
}
```

#### 8.5.1.4 Retrieving only the first GUIDed HOB with a specific GUID in the HOB list

```c
EFI_HOB_GENERIC_HEADER *Hob;
VOID *HobData;
UINTN HobDataSize;
Hob = GetFirstGuidHob (&gAbcGuid);
if (Hob != NULL)
{
  HobData = GET_GUID_HOB_DATA (Hob);
  HobDataSize = GET_GUID_HOB_DATA_SIZE (Hob);
  //
  // Operation on the HOB can be added here.
  //
}
```

#### 8.5.1.5 Traversing GUIDed HOBs with a specific GUID in the HOB list

```c
EFI_HOB_GENERIC_HEADER *Hob;
VOID *HobData;
UINTN HobDataSize;
Hob = GetHobList ();
while ((Hob = GetNextGuidHob (&gAbcGuid, Hob)) != NULL)
{
  HobData = GET_GUID_HOB_DATA (Hob);
  HobDataSize = GET_GUID_HOB_DATA_SIZE (Hob);
  //
  // Operation on the HOB can be added here.
  //
  // At the end of loop, GET_NEXT_HOB must be added here.
  // GetNextHob (
       HobType, HobStart) does not skip the HOB passed by
       // parameter HobStart. It returns HobStart back if HobStart itself
       // meets the requirement. So it is required to use GET_NEXT_HOB() to
       // skip current HOB. Otherwise, it would be in dead loop.
       //
       Hob = GET_NEXT_HOB (Hob
       );
}
```

### 8.5.2 Variable

A non-volatile variable can serve as a channel to pass data from DXE to PEI.
Because only a DXE driver can write a variable, and PEIM can only read
variables, this channel from DXE to PEI is also a one-way channel.

### 8.5.3 Dynamic PCD

A non-volatile dynamic PCD is also a high-level mechanism for communication
between a DXE driver and a PEIM.

Please refer to in Appendix A.
