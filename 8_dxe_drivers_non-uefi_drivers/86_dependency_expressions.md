<!--- @file
  8.6 Dependency Expressions

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

## 8.6 Dependency Expressions

A dependency expression specifies the protocols that the DXE driver requires to
execute. In EDK II, it is specified in the `[Depex]` section of INF file.

**********
**Note:** The PI Specification also defines an a priori file as an arbitrary
way for a firmware volume to specify driver execution order. Dependency
expressions for drivers covered by the apriori file are ignored.
**********

Following is an example of a [Depex] section:

**[Depex]**

**gEfiSimpleTextOutProtocolGuid AND gEfiHiiDatabaseProtocolGuid AND
gEfiVariableArchProtocolGuid AND gEfiVariableWriteArchProtocolGuid**

The example specifies that this driver can be executed only after all the four
protocols listed have been installed.

**********
**Note:** The four protocols in this example are necessary conditions, not
sufficient conditions. More dependency requirement smay be inherited. Details
follows.
**********

Module writers must pay special attention to two points on dependency
expressions.

* A DXE driver inherits dependency expressions from all library instances it
  links with. The dependency expression listed in the module INF is a subset of
  the dependency section in the PE32+ image built from this module. Linked
  library instances are specified in DSC file.

or

* A "non-UEFI driver model" driver's INF must have a dependency section. If
  TRUE is in INF's dependency section, because of inheritance, the generated
  dependency expression maybe not the TRUE.

The EDK II build tool would wipe out the dependency section in PE32+ image when
it has exactly all architectural protocols.
