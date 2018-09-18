<!--- @file
  3.8 Debugging a Module

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

## 3.8 Debugging a Module

### 3.8.1 Required steps for debugging a module

The following steps are required before starting to debug a module.

* "Build -b DEBUG" command

  EDK II supports generating DEBUG/RELEASE target. A different target causes
  different build options. The "BuildTarget" field in target.txt works with the
  "ToolChain" field to determine the actual path of the compiler tool-chain and
  build option. Developers can directly open $(WORKSPACE)\Conf\target.txt 
  and change "TARGET = DEBUG" for the debug tip. Developers also can use the command line to override this value, such as "build -b DEBUG" for debug tip.

* Choose the proper DebugLib library instance

  For the DebugLib library class, MdePkg and IntelFrameworkModulePkg core
  packages provide several library instances, which include `BaseDebugLibNull`,
  `BaseDebugLibSerialPort`, `UefiDebugLibConOut`, `UefiDebugLibStdErr`, and
  `PeiDxeDebugLibReportStatusCode`. Developers can choose proper DebugLib library
  instance in the package DSC file according to the actual requirements.

* Configure the Pcds consumed by DebugLib

  The DebugLib library class header defines two PCDs to be used for debug library
  configuration.
  
  The PCDs related to debug ability include `PcdDebugPropertyMask` and
  `PcdDebugPrintErrorLevel`. The former is used to control enable/disable
  print/assert abilities, and determines if the ASSERT macro is implemented
  through CpuDeadLoop or BreakPoint. For the latter, developers can set various
  values to control if the error information should be printed or filtered.

* Change build option

  Developers can modify or override the module build option. For example, a
  developer can use the "/Od" option for the Microsoft* compiler to disable the
  optimization of the compiler and avoid disordered instructions. The debug tip
  can also use the "/FAsc" option for the Microsoft* compiler to generate a
  source and assembly (.cod) file to help debug.

### 3.8.2 Basic debugging methods

Following are three basic methods for debugging:

* Using DEBUG print statement.

In EDK II project, there is a set of PCD to enable/disable debug capability.
Developer can turn on the functionality when starting to debug. Therefore, the
DEBUG print statements can be used to get information desired.

* CpuDeadLoop()

Developers can use an API to halt control flow, which is helpful to find the
location of an issue quickly.

* Module's Map file

Currently, EDK II generates a corresponding FV map file for every module.
Developers can depend on the base address of a loaded module and map file to
calculate the memory address of functions.
