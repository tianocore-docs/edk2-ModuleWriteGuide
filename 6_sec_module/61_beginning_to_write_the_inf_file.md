<!--- @file
  6.1 Beginning to Write the INF File

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

## 6.1 Beginning to Write the INF File

The following is a sample for `[Defines]` section of the SEC module:

```ini
[Defines]
  INF_VERSION    = 0x00010005
  BASE_NAME      = SampleSec
  FILE_GUID      = XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
  MODULE_TYPE    = SEC
  VERSION_STRING = 1.0
  ENTRY_POINT    = _ModuleEntryPoint
```

For a physical platform, `MODULE_TYPE` must be set to `SEC`. For an Emulation
Platform, the SEC module's `MODULE_TYPE` must be set to `SEC` or `USER_DEFINED`.

For IA-32 Intel Architecture `_ModuleEntryPoint` is the default entry point for
the SEC module.

For Itanium Processor Family platform, the entry point is configurable, such as
SAMPLE_ENTRY. Nevertheless, this entry point should be added in
`[BuildOptions]` section as following,

```ini
[Defines]
  ENTRY_POINT = SAMPLE_ENTRY

[BuildOptions]
  INTEL:*_*_IPF_DLINK_FLAGS = /ENTRY: SAMPLE _ENTRY
  MSFT:*_*_IPF_DLINK_FLAGS  = /ENTRY: SAMPLE _ENTRY
  GCC:*_*_IPF_PP_FLAGS      = --entry _ SAMPLE _ENTRY
```

The implementation of the SEC entry point is commonly in the assembly language.
