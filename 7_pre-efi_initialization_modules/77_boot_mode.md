<!--- @file
  7.7 Boot Mode

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

## 7.7 Boot Mode

Sometime, PEIMs need to determine the boot mode (e.g. S3, S5, normal boot,
diagnostics, etc.) and take appropriate actions depending on it. For example,
the VariablePei module will not install EFI ReadOnlyVariable2Ppi in the
recovery boot path.

The Pei Service Table provides one pair of services to Set or Get the mode.

Accordingly, the Pei Service Library APIs are: SetBootMode() and GetBootMode().

The following is one example of how to get boot mode:

```c
//
// Check if this is recovery boot path. If no, publish the variable
// access capability to other modules. If yes, the content of variable
// area is not reliable. Therefore, in this case we should not provide
// variable service to other pei modules.
//
Status = PeiServicesGetBootMode (&BootMode);
ASSERT_EFI_ERROR (Status);
if (BootMode == BOOT_IN_RECOVERY_MODE)
{
  return EFI_UNSUPPORTED;
}
Status = PeiServicesInstallPpi (&mPpiListVariable);
Note:
The PI Specification lists all possible boot modes.
```
