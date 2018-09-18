<!--- @file
  3.7 Building the module

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

## 3.7 Building the module

After the module source is finished, the module INF is added into the DSC file
to be built to the expected binary image. Library, EFI and OptionRom images are
supported by the EDK II build system.

### 3.7.1 Add the module INF in package DSC

To build a module, the module INF file is specified in DSC [Components]
section. Its relative file path from the workspace (beginning from the package
directory), up to and including the INF file name, is added per the following
example. Some module may be required to be built for the specific ARCH.

The DSC [Defines] section lists all supported architectures for this platform.
The [Components.ARCH] section lists the modules for this architecture. The ARCH
must be on the list of all ARCHs from the [Defines] section. The separate
[Components] section can be created for the modules that support the different
architectures.

#### 3.7.1.1 Example: Package.dsc Components

```ini
[Defines]
  ......
  SUPPORTED_ARCHITECTURES = IA32|IPF|X64|EBC
  ......

[Components] ## All libraries, drivers and applications may be added here to be built.
  ## this library will be built to the IA32, IPF, X64 and EBC arch version.
  PackageNamePkg/Library/NameOneLib/NameOneLib.inf

[Components.IA32] ## This PEI driver will be built to the IA32 arch version.
  PackageNamePkg/NameTwoPei/NameTwoPei.inf

[Components.X64, Components.EBC] ## This DXE driver will be built to the X64 and EBC arch version.
  PackageNamePkg/NameOneDxe/NameOneDxe.inf
```

### 3.7.2 Select Library Instances

**********
**Note:** Skip this step if the module is a library instance.
**********

For drivers and applications, a library instance for each library class
dependency must be selected and linked to its binary EFI image.

The module INF [LibraryClasses] section lists all required library classes,
which are produced by library instances.

Library instances are implemented for the different purposes. Most of them
abstract the generic logic as the common interfaces for the crossing platform
modules. Some are for performance and size optimization.

For example, in the MdePkg the BaseMemoryLibOptDxe instances produce the "good
performance" BaseMemory library class implementation based on the registers to
perform a memory operation. Another example, in the MdePkg the PeiIoLibCpuIo
library instance implements the Io library class by using the services of CpuIo
PPI to reduce code size.

Depending on platform requirements, different library instances can be set in
the DSC [LibraryClasses] section. In the initial development, the generic
library instances without any optimization are often used to reduce the
development risk. After the module basic functionality is finished, it can be
further tuned for size and performance.

The EDK II MdePkg provides many common library instances for user selection.
The details for each library instance are found in its INF file or in the
MdePkg specification.

The following example lists the most basic library instances.

#### 3.7.2.1 Example Package.dsc LibraryClasses

##### 3.7.2.1.1 Example 1: Generic library instances

```ini
[LibraryClasses]
  ## Basic Library
  BaseLib|MdePkg/Library/BaseLib/BaseLib.inf
  DebugLib|MdePkg/Library/BaseDebugLibNull/BaseDebugLibNull.inf
  SynchronizationLib|MdePkg/Library/BaseSynchronizationLib/BaseSynchronizat ionLib.inf
  CpuLib|MdePkg/Library/BaseCpuLib/BaseCpuLib.inf
  BaseMemoryLib|MdePkg/Library/BaseMemoryLib/BaseMemoryLib.inf
  PrintLib|MdePkg/Library/BasePrintLib/BasePrintLib.inf
  PcdLib|MdePkg/Library/BasePcdLibNull/BasePcdLibNull.inf
  ## Pci Library
  PciCf8Lib|MdePkg/Library/BasePciCf8Lib/BasePciCf8Lib.inf
  PciExpressLib|MdePkg/Library/BasePciExpressLib/BasePciExpressLib.inf
  PciLib|MdePkg/Library/BasePciLibCf8/BasePciLibCf8.inf
  ## Entry Point Library
  PeimEntryPoint|MdePkg/Library/PeimEntryPoint/PeimEntryPoint.inf
  UefiDriverEntryPoint|MdePkg/Library/UefiDriverEntryPoint/UefiDriverEntryP oint.inf
  UefiApplicationEntryPoint|MdePkg/Library/UefiApplicationEntryPoint/UefiAp plicationEntryPoint.inf
  ## PEI service library
  PeiServicesLib|MdePkg/Library/PeiServicesLib/PeiServicesLib.inf
  PeiServicesTablePointerLib|MdePkg/Library/PeiServicesTablePointerLib/PeiS ervicesTablePointerLib.inf
  ## UEFI and DXE service library
  UefiBootServicesTableLib|MdePkg/Library/UefiBootServicesTableLib/UefiBoot
  ServicesTableLib.inf
  DxeServicesTableLib|MdePkg/Library/DxeServicesTableLib/DxeServicesTableLi b.inf
  UefiRuntimeServicesTableLib|MdePkg/Library/UefiRuntimeServicesTableLib/Ue fiRuntimeServicesTableLib.inf
  DxeServicesLib|MdePkg/Library/DxeServicesLib/DxeServicesLib.inf
  UefiRuntimeLib|MdePkg/Library/UefiRuntimeLib/UefiRuntimeLib.inf
  UefiLib|MdePkg/Library/UefiLib/UefiLib.inf
  DevicePathLib|MdePkg/Library/UefiDevicePathLib/UefiDevicePathLib.inf
  ## This library instance should be provide by chipset.
  TimerLib|MdePkg/Library/BaseTimerLibNullTemplate/BaseTimerLibNullTemplate
  .inf
```

The library instances given above are generic ones for use in all drivers.
However, according to the module type and CPU architecture, more suitable
library instances can be added into [LibraryClass.ARCHs.ModuleType] section to
override the common setting, per the following example:

##### 3.7.2.1.2 Example 2: library instances per module type and CPU architecture

```ini
[Defines]
  ......

[LibraryClasses]
  ......

[LibraryClasses.IA32, LibraryClasses.X64]
  ## these two optimized library istances only for X86 arch.
  ## they will override the above common base memory instance.
  MdePkg/Library/BaseMemoryLibOptDxe/BaseMemoryLibOptDxe.inf
  MdePkg/Library/BaseMemoryLibOptPei/BaseMemoryLibOptPei.inf

[LibraryClasses.common.UEFI_DRIVER]
  # these two library intances are set for UEFI driver type module.
  #
  # Debug library instance will override the above NULL instance.
  MemoryAllocationLib|MdePkg/Library/UefiMemoryAllocationLib/UefiMemoryAllo cationLib.inf DebugLib|MdePkg/Library/UefiDebugLibConOut/UefiDebugLibConOut.inf
```

For the specific requirement, a driver may select its library instances to
override all library instances specified in the [LibraryClasses] section. This
chosen library instance is set only for this driver. Such usage is also
supported in the DSC as follows:

##### 3.7.2.1.3 Example 3: library instances for a specific driver

```ini
[Defines]
  ......

[LibraryClasses]
  ......

[Components]
  ## For NameOnDxe driver, its linked PCD library instance is DxePcdLib, not
  ## the above BasePcdLibNull instance.
  PackageNamePkg/NameOneDxe/NameOneDxe.inf {
  <LibraryClasses>
  PcdLib|MdePkg/Library/DxePcdLib/DxePcdLib.inf
  }
```

### 3.7.3 Configure PCDs

**********
**Note:** Skip this step for library modules
**********

Modules that consume PCDs (including those consumed by linked libraries) need
to have those PCDs configured in the DSC. The configured PCDs will be applied
both to the module and to its linked library instances. PCDs are declared in
package DEC file. When the PCD's value is the same as the default value defined
in the DEC, those PCDs need not be specified in DSC again.

In the DSC, the PCD type and value can be configured according to the platform
requirements. The PCD type must be set to single type in a DSC file. If not
specified in DSC, the PCD type will be same as its declaration PCD type in the
package DEC file. If a PCD is declared to support multiple PCD types, the
default PCD type is a fixed PCD.

The PCD value may set the different values for the different drivers. If its
value is not specified, the value will be from its declaration default value to
the chosen PCD type in the package DEC file.

#### 3.7.3.1 PCD types

* PcdsFeatureFlag,

* PcdsFixedAtBuild,  PcdsPatchableInModule,  PcdsDynamic.

#### 3.7.3.2 Feature Flag PCD

If a PCD is declared as PcdsFeatureFlag, it must be of the FeatureFlag PCD type
and BOOLEAN data type. When this type of PCD is used in a module, it must be
specified in the [FeaturePcd] section of the module INF.

**********
**Note:** Only FeaturePcdGet API can access this PCD type.
**********

#### 3.7.3.3 Fixed PCD

If a PCD value is decided during the build time, its type can be set to

PcdsFixedAtBuild. When this PCD type is used in module, it can be specified in
the [FixedPcd] or [PCD] section of the module INF. In addition, FixedPcdGet and
PcdGet API can be used to access this type PCD in the module source code.

When FixedPcdGet API is used, this type PCD can be used as the array index in a
driver.

**********
**Note:** For a library, no such usage is supported.
**********

#### 3.7.3.4 Patchable PCD

If the PCD value needs to be modified in the binary image, its type will be

PcdsPatchableInModule. When this type PCD is used in module, it can be
specified in the [FatchPcd] or [PCD] section of the module INF. In addition,

PatchPcdGet/PatchPcdSet and PcdGet/PcdSet API can be used to access this type
PCD in the module source code.

#### 3.7.3.5 Dynamic PCD

If PCD value is obtained from the runtime environment, its type must be
Dynamic. If a dynamic PCD is from a PCD database that shares data between
drives, its type will be PcdsDynamicDefault. If a dynamic PCD is related to a
UEFI variable, its type will be PcdsDynamicHII.

When this type PCD is used in a module, it must be specified in the [PCD]
section of the module INF. Only PcdGet/PcdSet API can be used to access this
type PCD in module source code.

Dynamic type PCDs must be configured in the DSC file to set the dynamic type
and the initial value for the whole platform, which cannot inherit from its
declaration DEC file and cannot be overridden by a driver.

The following example gives each type of PCD setting:

##### 3.7.3.5.1 Package.dsc PCDs showing each type of PCD setting

```ini
[PcdsFeatureFlag]
  #PcdName | Pcdvalue
  gEfiMdeModulePkgTokenSpaceGuid.PcdHelloWorldPrintEnable|TRUE

[PcdsFixedAtBuild]
  #StringPcdName | StringValue| StringType| StringMaxSize
  gEfiMdeModulePkgTokenSpaceGuid.PcdHelloWorldPrintString |L"UEFI Hello
  World!n"|VOID*|100

[PcdsPatchableInModule]
  #Pring level can be modifed in binary image
  gEfiMdePkgTokenSpaceGuid.PcdDebugPrintErrorLevel|0x80000046

[PcdsDynamicDefault]
  #Default print times is 1, its can be modifed in runtime.
  gEfiMdeModulePkgTokenSpaceGuid.PcdHelloWorldPrintTimes|0x1

[PcdsDynamicHii]
  #time out
  #PcdName | Uefi Variable name | Uefi Variable Guid | Offset | Default value
  gEfiIntelFrameworkModulePkgTokenSpaceGuid.PcdPlatformBootTimeOut|L"Timeou t"|gEfiGlobalVariableGuid|0x0|5
```

PCD section also supports ARCH option to set PCDs value only for a specific
ARCH image. It can be set in [PcdsType.ARCHs] section. For example:

##### 3.7.3.5.2 Example: Package.dsc PCDs for a specific ARCH image

```ini
[PcdsFixedAtBuild.IPF]
gEfiMdePkgTokenSpaceGuid.PcdIoBlockBaseAddressForIpf|0x0ffffc000000
```

PCD value can be also set only for a driver to override the PCD section
setting. However, Dynamic type PCD must be set as the global value, which
cannot be overridden by a driver. Such usage is also supported in DSC like:

##### 3.7.3.5.3 Example: Package.dsc dynamic PCDs

```ini
[Components]
  # For NameOnDxe driver, its print level PCD value is 0x80000000, not same to
  # the above setting 0x80000046.
  PackageNamePkg/NameOneDxe/NameOneDxe.inf {
  <PcdsPatchableInModule>
  gEfiMdePkgTokenSpaceGuid.PcdDebugPrintErrorLevel|0x80000000 }
```

### 3.7.4 Customize Build Options

Build options are the different compiler options to build the image under the
different tool chain. They are defined in $(WORKSPACE)/Conf/tools_def.txt file.
This file provides the common compiler options for each tool chain tag. The
compiler options are grouped into two main types: compile option and link
option. The full option list can refer to tools_def.txt and _EDK II Build
Specification_.

The following example lists the usual compiler and link option.

#### 3.7.4.1 Example: Tools_def.txt

When the common build options in tools_def.txt do not satisfy the development
requirement, they can be extended or replaced.

```ini
  #
  # Build option syntax
  # TARGET_TOOLCHAIN_ARCH_COMMANDTYPE_ATTRIBUTE = build option
  #
  # TARGET is RELEASE or DEBUG
  # TOOLCHAIN is tool tag name, MYTOOLS is a tag with VS2005 tool chain
  # ARCH is the tool cpu family architecture.
  # COMMANDTYPE is the build option name. CC is compile, DLINK is link.
  # ATTRIBUTE is FLAGS for the build option.
  #
  # Debug related options of VS2005 compiler
  DEBUG_MYTOOLS_IA32_CC_FLAGS    = ... /Zi /Gm
  DEBUG_MYTOOLS_IA32_DLINK_FLAGS = ... /DEBUG
```

The EDK II build system provides four levels of override mechanisms to
customize the compiler options. The options override each other in the order
given.

#### 3.7.4.2 Modifying Tools_def.txt

Directly modify build options in tool_def.txt, which changes the compiler
options and affects all modules and platforms in same workspace.

#### 3.7.4.3 Modifying an INF file

Add the additional compiler option in module INF [BuildOptions], which applies
for this module to be built in any build DSC.

##### 3.7.4.3.1 Example: Module.inf

```ini
[BuildOptions]
  #
  # Tool Chain Family: MSFT, INTEL, GCC for the different compiler tools.
  # The different compiler tools have the different compiler options.
  # * is not specific TARGET, TOOLTAGNAME, ARCHs.
  #
  # '=' append the additional option to the tail.
  # Append /FAsc compile option only for this module
  #
  MSFT:*_*_*_CC_FLAGS = /FAsc
  #
  # '==' replace all options by using new setting
  #
  MSFT:*_*_*_DLINK_FLAGS = = /DEBUG
```

#### 3.7.4.4 Modifying DSC platform options

Add the additional compiler option in build DSC [BuildOptions] section, which
will update the compiler options for all modules described in same DSC.

##### 3.7.4.4.1 Example: Package.dsc BuildOptions -compler options for all modules

```ini
[BuildOptions]
  #
  # Append /Od Compile option in DSC to disable optimiaztion for all modules
  #
  MSFT:*_*_*_CC_FLAGS = /Od
```

#### 3.7.4.5 Modifying a DSC for a single module

Add the additional compiler option in the build DSC [Components] section for a
module, which applies for it only in this build DSC.

##### 3.7.4.5.1 Example: Package.dsc BuildOptions-single module compiler options

```ini
[Components]
  #
  # Append the debug compile option only to NameOneDxe driver
  #
  PackageNamePkg/NameOneDxe/NameOneDxe.inf {
  <BuildOptions>
  MSFT:*_*_*_CC_FLAGS = /Od
  }
```

The higher-level setting will append new options in the tail or replace all
options. The four methods work together meet the platform build requirements.

**********
**Note:** The last two usages are recommended. Both only modify the DSC file.
**********

### 3.7.5 Build module image

After the settings given above, the EDK II build command can be called to build
the module to the binary image. It has many build configurations to support the
differing build requirements. The usual used build options are introduced in
the following manner:

#### 3.7.5.1 Example: Build option

**Build -p Package.dsc -m Module.inf -a ARCH -b TARGET -t TOOLTAG**

#### 3.7.5.2 Build Package (-p option)

All modules in the [Components] section of the specified package DSC will be
built if the build module option is not added. If specified more than once on
the command line, the final selection is used.

##### 3.7.5.2.1 Example: Build -p option

```ini
  # Build all modules in PackageOne DSC
  Build -p PackageOnePackageOne.dsc
  # Build all modules in PackageTwo DSC
  Build -p PackageOnePackageOne.dsc -p PackageTwoPackageTwo.dsc
```

#### 3.7.5.3 Build Module (-m option)

When a single specified module is built, it must be in the [Components] section
of the specified DSC. If this option is not added, all modules in the DSC will
be built. If specified more than once on the command line, the final selection
is used.

##### 3.7.5.3.1 Example: Build -m option

```ini
  # Build single module One in PacakgeOne DSC
  Build -p PackageOnePackageOne.dsc -m PackageOneOneOne.inf
  # Build single module Two in PackageOne DSC
  Build -p PackageOnePackageOne.dsc -m PackageOneOneOne.inf -m
  PackageOneTwoTwo.inf
```

#### 3.7.5.4 Build ARCH (-a option)

The supported ARCH option is IA32, X64, IPF and EBC. New arch types may be
added in the future. The module with the settings given above will be built to
the specified ARCH. If specified more than once on the command line, each ARCH
is built sequentially.

##### 3.7.5.4.1 Example: Build -a option

```ini
  # Build all modules in PacakgeOne DSC to IA32 arch
  Build -p PackageOnePackageOne.dsc -a IA32
  # Build all modules in packageOne DSC to IA32 and X64 arch both
  Build -p PackageOnePackageOne.dsc -a IA32 -a X64
```

#### 3.7.5.5 Build Target (-b option)

The supported target is DEBUG and RELEASE, which are for the different compiler
option settings. The module will be built under the specified target. If
specified more than once on the command line, each Target is built sequentially.

##### 3.7.5.5.1 Example: Build -b option

```ini
  # Build all modules in PacakgeOne to IA32 arch
  Build -p PackageOnePackageOne.dsc -b DEBUG
  # Build all modules in packageOne to IA32 and X64 arch both
  Build -p PackageOnePackageOne.dsc -b DEBUG -b RELEASE
```

#### 3.7.5.6 Build Tool Tag Name (-t option)

Tool tag name are defined in Conf\Tools_def.txt file to represent a compiler
tool chain. For example, MYTOOLS is a default tool tag name to Microsoft VS2005
tool chain. The module will be built by the specified tool chain. If specified
more than once on the command line, each used tool chain is used sequentially.

##### 3.7.5.6.1 Example: Build -t option

```ini
  # Build all modules in PacakgeOne by MYTOOLS tool chain
  Build -p PackageOnePackageOne.dsc -t MYTOOLS
  # Build all modules in packageOne by MYTOOLS and ICC tool chain
  Build -p PackageOnePackageOne.dsc -t MYTOOLS -t ICC
```

If the options given above are not specified with the build command, their
default settings will be from Conf\target.txt file. Details of build command
are referenced in the _EDK II User Manual_ 3.2.2 section.

After applying the settings and build given above, the library and EFI image
can be generated into the build output directory. The build output directory is
introduced in detail in _EDK II User Manual_, section 3.3 The library is
generated into the OUTPUT directory, and the EFI image is generated into the
DEBUG directory.

#### 3.7.5.7 Example: Build HelloWorld

```
Build -p MdeModulePkg/MdeModulePkg.dsc -m
MdeModulePkg/Application/HelloWorld/HelloWorld.inf -a IA32 -b DEBUG -t
MYTOOLS
HelloWorld.efi will be generated in DEBUG directory:
$(WORKSAPCE)/Build/MdeModulePkg/DEBUG_MYTOOLS/IA32/MdeModulePkg/Applicati on/HelloWorld/HelloWorld/DEBUG, OUTPUT
In the build DEBUG directory, the following files are created: the EFI image, intermediate files, AutoGen.h, AutopGen.c and the Module.map file.
AutoGen.h and AutopGen.c files are generated for each module by the EDK II build tool based on the required module information. They declare the dependent PCDs, Guid Values and include the module entry point related functions. Those AutoGen functions are referred to in the ModuleEntryPoint library instance. For each module, the entry point function first calls AutoGen code, then enters into module functions.
```

Module.map is generated by a compiler tool to list all functions and their
relative addresses in this module. They can be used to locate the module
function address at run time.

### 3.7.6 Build EFI Option Rom image

An EFI Option Rom image is a standard EFI image. It can be built by the build
module command mentioned in the section given above. The only difference is
that its INF includes the related PCI option in [Defines] section. When PCI
option is set in the module INF, this module will be built to both EFI and
Option Rom images. In the build DEBUG directory, ModuleName.efi and
ModuleName.rom will be generated.

The following example contains all PCI options required to create the EFI
option rom image.

#### 3.7.6.1 Example: OptionRom INF-all PCI options

```ini
[Defines]
  INF_VERSION = 0x00010005
  BASE_NAME   = OptionRomOne
  FILE_GUID   = XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
  MODULE_TYPE = UEFI_DRIVER
  ENTRY_POINT = UefiMain
  # PCI option for VendorId, DeviceId, ClassCode and Revision
  #
  # PCI option is used to create PCI option rom image.
  PCI_VENDOR_ID  = 0x8086
  PCI_DEVICE_ID  = 0x29c2
  PCI_CLASS_CODE = 0x030000
  PCI_REVISION   = 0x1000

[Sources.common]
  OptionRom.c

[Packages]
  MdePkg/MdePkg.dec

[LibraryClasses]
  UefiBootServicesTableLib
  UefiDriverEntryPoint
  UefiLib
  DebugLib
```

Of the PCI options, VendorId, DeviceId and ClassCode are required.The PCI
revision is optional. If the PCI revision is not specified, the default
revision is 0x0.

### 3.7.7 Common build module breaks

The following lists the common build module breaks and their fixes.

**error 4000: Value of Guid [gCNameGuid] is not found under [Guids] section in
MdePkg\MdePkg.dec**

The module INF is missing a package in the [Packages] section. The same error
can happen for any Guid, Ppi, Protocol, LibraryClass and PCD used by this
module. The package DEC that declares them must be added to the [Packages]
section of this module INF.

**error LNK2001: unresolved external symbol _gCNameGuid**

The module INF is missing a Guid in the [Guids] section. The same error can
happen for any Guid, Ppi, Protocol, LibraryClass and PCD used by this module.
The Guid, Ppi, or Protocol) CName needs to be added into the [Guids], [Ppis],
or [Protocols] sections, respectively.

**error LNK2001: unresolved external symbol _LibraryFunctionName**

The module INF is missing a library class in the [LibraryClasses] section. This
prevents the library instance from being linked to the module. The library
class that includes LibraryFunctionName must be added into the [LibraryClasses]
section of this module INF.

```
warning C4013: 'FeaturePcdGet' undefined; assuming extern returning int
PcdLib.h is missing in the module source code. When a PCD is used in a module, PcdLib APIs are referenced to access the PCD. The PcdLib header file must included in this module source code.
error 0010: File name case mismatch MdeModulePkg\Application\Helloworld\HelloWorld.inf MdeModulePkg\Application\HelloWorld\HelloWorld.inf [in file system]
```

**********
**Note:** Lower case 'w' in' Helloworld', in the first path
**********

The module INF file path specified in the DSC is not same as its file path in
the file system. The same error may occur for the source file path specified in
the [Source] section of a module INF. According to the error information, the
file path in the DSC or INF needs to be corrected to its file path in file
system. All files must have their name and case set the same in the metadata
files as in the file system.

**error 4000: Instance of library class [NameOneLib] is not found consumed by
module [MdeModulePkg\Application\HelloWorld\HelloWorld.inf]**

The DSC file is missing a library class to library instance mapping for the
given library. If the module does not depend on the library class, the unused
library class can be removed from the [LibraryClasses] section of module INF to
fix this error. If the module requires this library class, the corresponding
library instance mapping must be added into the [LibraryClasses] section of the
DSC file.
