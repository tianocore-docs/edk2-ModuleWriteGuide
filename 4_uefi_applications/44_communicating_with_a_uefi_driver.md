<!--- @file
  4.4 Communicating with a UEFI driver

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

## 4.4 Communicating with a UEFI driver

### 4.4.1 Protocol

Uefi Application can use the following protocol service to access the protocol
interfaces produced by UEFI drivers.

Services to retrieve the protocol:

* `LocateProtocol()`

* `HandleProtocol()`

* `OpenProtocol()`

**********
**Note:** Uefi Application cannot use the InstallProcotol service or
corresponding Libraries to install the protocol. This is because the UEFI
application is unloaded after returning from the entry point. Therefore, it is
meaningless to install this protocol.
**********

### 4.4.2 Variable

Variables are defined as key/value pairs that consist of identifying
information plus the attributes (the key) and arbitrary data (the value).
Variables are intended for use as a means to store data that is passed between
the EFI environment implemented in the platform and EFI OS loaders and other
applications that run in the EFI environment.

UEFI application can read and write variable via UEFI Runtime Services

`GetVariable()` and `SetVariable()`. Because UEFI application must run after
the Dxe/UEFI driver, Variable Arch protocol must be installed.
