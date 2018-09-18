<!--- @file
  Appendix A Dynamic PCD

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

# Appendix A Dynamic PCD

The dynamic type PCD is used for a configuration/setting whose value is to be
determined dynamic. In contrast, the value of static type PCD (FeatureFlag,
FixedPcd, PatchablePcd) is fixed in the final generated FD image during build
time.

The "dynamic" determination means one of three things:

* The PCD setting value is produced and consumed by drivers during execution.

* The PCD setting value is user configurable from setup.

* The PCD setting value is produced by the platform OEM vendor in a specified
  area.

### _A.1.1 Class of Dynamic Type_

According to module distribution way, dynamic PCD could be classified as:

**Dynamic:**

If module is released in source code and will be built with platform DSC, the
dynamic PCD used by this module can be accessed as:

`PcdGetxx(PcdSampleDynamicPcd);`

In building platform, the build tools translate PcdSampleDynamicPcd to the
parameters Token Space Guid: Token Number for this PCD.

**DynamicEx:**

If a module is released as binary and is not included in the platform build,
the dynamic PCD used by this module must be accessed as:

PcdGetxxEx(gEfiMyTokenspaceGuid, PcdSampleDynamicPcd)

**********
**Note:** Developers need to explicitly pass Token SpaceGuid and TokenNumber as
the parameters.
**********

According to PCD value's storage method, dynamic PCD may be classified three
ways:

**Default Storage:**

The PCD value is stored in PCD database maintained by PCD driver in boot-time
memory.

This type is used for communication between PEIM/DXE drivers and DXE/DXE
drivers. All set/get value are lost after boot-time memory is turn off.

```ini
[PcdsDynamicDefault] is used as the section name for this type of PCD in the platform DSC file.

[PcdsDynamicExDefault] is used for dynamicEx types of PCDs.
  Variable Storage:
```

The PCD value is stored in a variable area. As the default storage type, this
type of PCD could be used for PEI/DXE driver communication. Beside that, this
type PCD could also be used to store the value associate with a HII setting via
variable interface.

In PEI phase, the PCD value can be obtained but not set because the variable
area is read only.

```ini
[PcdsDynamicHii] is used as section name for this type of PCD in the platform DSC file.

[PcdsDynamicExHii] is for the dynamicEx type of PCD.
  OEM specified storage area:
```

The PCD value is stored in an OEM-specified area whose base address is
specified by the FixedAtBuild PCD setting PcdVpdBaseAddress.

The area is read only for PEI and DXE phases.

```ini
[PcdsDynamicVpd] is used as section name for this type PCD in the platform DSC file.

[PcdsDynamicExVpd] is for a dynamicex type of PCD.
```

### _A.1.2 When and how to use dynamic PCD_

Module developers do not care if the PCD is dynamic or static when writing
source code/INF. Dynamic PCD and dynamic type are indicated by the platform
integrator in the platform DSC file.
