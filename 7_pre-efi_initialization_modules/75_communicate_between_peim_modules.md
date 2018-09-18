<!--- @file
  7.5 Communicate between PEIM Modules

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

## 7.5 Communicate between PEIM Modules

There are three methods for PEIMs to communicate with each other: PPIs, HOBs
and dynamic PCDs.

### 7.5.1 PPI

PEIM modules can communicate with each other using a structure called a
PEIM-toPEIM Interface (PPI). Each PPI has one GUID. The Pei Service Table
provides some Pei services to access the PPI database.

In EDK II, one PEIM module can invoke `PeiServicesInstallPpi()` to publish its
PPI services into the PPI database by GUID. Another PEIM module can invoke
`PeiServicesLocatePpi()`to locate PPI services from the PPI database by GUID.

#### 7.5.1.1 Installing a PPI

For example, if Module A wants to publish one Template PPI service (including
three APIs: `Interface2`, `Interface2` and `Interface3`), it can install the
Template PPI by using `PeiServicesInstallPpi.` For example:

```c
//
// Template PPI
//
EFI_PEI_TEMPLATE_PPI gEfiTemplatePpi = {
  Interface1,
  Interface2,
  Interface3
};
EFI_PEI_PPI_DESCRIPTOR gPpiListTemplatePpi = {
  (EFI_PEI_PPI_DESCRIPTOR_PPI | EFI_PEI_PPI_DESCRIPTOR_TERMINATE_LIST),
  &gEfiTemplateGuid,
  &gEfiTemplatePpi
};
EFI_STATUS
PeimEntryPoint (
  IN EFI_FFS_FILE_HEADER  *FfsHeader,
  IN EFI_PEI_SERVICES     **PeiServices
  )
{
  EFI_STATUS  Status;
  //
  // Publish Template PPI.
  //
  Status = PeiServicesInstallPpi (&gPpiListTemplatePpi);
  return Status;
}
```

#### 7.5.1.2 Locating a PPI

If Module B needs to invoke `Interface2()` provided by Template PPI, it can
locate Template PPI by using the following code:

```c
//
// Get Template PPI
//
Status = PeiServicesLocatePpi (
           &gEfiTemplateGuid,
           0,
           NULL,
           (VOID **)&TemplatePpi
           );
ASSERT_EFI_ERROR (Status);
//
// Invoke Interface2()
//
Status = TemplatePpi->Interface2 (...);
ASSERT_EFI_ERROR (Status);
```

### 7.5.2 HOB

PEIM modules can build a Hand-Off Block (HOB) to pass some information to the
DXE Foundation and DXE modules. In addition, other PEIMs can obtain similar
information from a HOB by using the HOB services in the Pei Service Table.

In EDK II, the Hob Library provides the generic interfaces to access HOBs for
PEIMs and DXE drivers.

### 7.5.3 PCD

A PEIM can communicate with other PEIMs through dynamic PCDs. As with HOBs,
only PEIMs can obtain dynamic PCDs values, which were previously set by DXE
drivers. The usage of getting PCDs is introduced in Appendix A, Dynamic PCD.
