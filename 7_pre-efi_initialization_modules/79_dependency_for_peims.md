<!--- @file
  7.9 Dependency for PEIMs

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

## 7.9 Dependency for PEIMs

A PEIM must have a dependency section. The PEIM is dispatched after all
conditions in the dependency section are met.

If a PEIM has a dependency section TRUE, it can be dispatched immediately. In
an extended INF file, a dependency section is contained in the `[Depex]`
section. PPI dependency is defined by the GUID of the PPI.

For example:

```[Depex]

gEfiPeiReadOnlyVariable2PpiGuid AND gEfiPeiCachePpiGuid AND
gPeiCapsulePpiGuid
```
This module may be dispatched only after Read Only Variable2 Ppi, CachePpi and
CapsultPpi are all installed successfully.

In the preceding example, the expression opcode `AND` is used to show the
logical relationship between GUIDs. See the _EDK II Extended INF Specification_
for complete details.

**********
**Note:** A PEIM inherits dependency expressions from all library instances it
links with. The dependency expression listed in module INF is a subset of the
dependency section in the PE32+ image built from this module.
**********

The PI specification also defines a generic rule to decide the dispatch order
for PEIMs: the apriori file. It complements the dependency expression mechanism
of the PEI Phase by stipulating a series of modules that must be dispatched in
a prescribed order. The `[depex]` sections for these modules are ignored.
