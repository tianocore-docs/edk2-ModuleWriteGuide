<!--- @file
  2.1 Introduction

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

## 2.1 Introduction

Each EDK II Package is a container that includes a set of modules and their
related definitions. Each Package is an EDK II distribution unit. It can be
used to manage and release the big project to facilitate a user's distribution
and reuse. Entire project sources can be split into different packages to
reduce the release granularity. The new project can also be made from released
packages from different sources.

### 2.1.1 EDK II Packages

A Package is a directory that organizes a group of modules with a single
package declaration file (DEC).

EDK II provides UEFI and PI compliant packages: MdePkg, MdeModulePkg, etc.

The MdePkg contains the complete definitions in EFI1.1, UEFI2.0, UEFI2.1, PI1.0

Specifications and all library classes and instances defined in EDK II MDE
(Module Development Environment) Library Specification. UEFI and PI drivers can
be developed based solely on this package.

The MdeModulePkg contains a group of cross-platform drivers that conform to
UEFI and PI specifications. They can be referred to when developing new UEFI
and PI drivers.

Detailed information of EDK II packages can be found in _EDK II User Guide,_
section 2.2 and in the package specification for each package.

### 2.1.2 The Package Directory

Each package has a unified directory structure that separate the different
source files. The root directories in each package are: Include, Library,
Application and Drivers.

* The include directory contains all public header files that are exposed by
  this package and are used by this package and other packages. Below the
  Include directory, subdirectories may be created to include Ppi, Protocol,
  Guid, Industry Standard and library class header files (when these header
  files become public).

* The library directory contains directories for each library instance module
  included.

* The application directory contains directories for each UEFI applications
  module included.

* The driver directory contains directories for each driver group and for each
  driver.

Each module (library instance, application and driver) has its own directory in
which to group its source files. A module may only depend on files under its
directory or on public header files. A module is not permitted to depend on
source files from another module directory.

#### 2.1.2.1 Sample directories and sub-directories in a package

* Package.dec Package declaration file

* Package.dsc Platform Package build description file

* Include Public header files

    * Protocol\ Public Protocol header files o Ppi\ Public PPI header files o
      Guid\ Public GUID header files

    * IndustryStandard\ Public Industry Standard header files o Library\ Public
      Library class header files

* Library\ Libraries instances

    * NameOneLib\ Library instance NameOne source files and INF o NameTwoLib\
      Library instance NameTwo source files and INF

* Application\ Uefi Applications o NameOneApp\ Application NameOne source files
  and INF o NameTwoApp\ Application NameTwo source files and INF  NameOneDxe\
  Dxe Driver NameOne source files and INF.

* NameTwoPei\ Pei Driver NameTwo source files and INF.

If no related source files exist in a package, the corresponding directory may
not be created. For example, if no application is provided in a package, a
blank Application directory is not required.

### 2.1.3 Package Declaration File

Each package has a single package declaration file (DEC) to define the
package's public interfaces. The public interfaces are the package's public
header files, GUIDs, and PCDs.

The DEC has Defines, Includes, LibraryClasses, Guids, Ppis, Protocols and Pcds
sections.

The [Defines] section defines the package name and package GUID.

The [Includes] section must list the root directory of public header file
directory.

The [LibraryClasses] section contains every library class header file in the
Package\Include\Library directory.

The [Guids] section specifies the Guid value for each Guid in the
Package\Include\Guid directory.

The [Ppis] section specifies the Guid value for each PPI in the
Package\Include\Ppi directory.

The [Protocols] section specifies the Guid for each Protocol in the
Package\Include\Protocol directory.

The PCDs are declared in different PCD sections according to their type
(FeatureFlag, FixedAtBuild, PatchableInModule, Dynamic, and DynamicEx). If a
PCD supports multiple PCD types, it must be declared in all supported type
sections. When a PCD is declared, its data type and default value must also be
specified.

The following is a sample DEC file, additional package public information may
be added.

#### 2.1.3.1 Example: Package.dec

```ini
[Defines]
  DEC_SPECIFICATION = 0x00010005
  PACKAGE_NAME      = PackageName
  PACKAGE_GUID      = xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
  PACKAGE_VERSION   = 0.1

[Includes]
  Include #Package Include directory

[LibraryClasses]
  ## Library class name is same to library header file name
  OneClassLib|Include/Library/OneClassLib.h

[Guids]

#GuidCName = {xxxxxxxx,xxxx,xxxx,{xx,xx,xx,xx,xx,xx,xx,xx}},
[Ppis]

#PpiGuidCName = {xxxxxxxx,xxxx,xxxx,{xx,xx,xx,xx,xx,xx,xx,xx}},
[Protocols]

#ProtocolGuidCName = {xxxxxxxx,xxxx,xxxx,{xx,xx,xx,xx,xx,xx,xx,xx}},
[PcdsFeatureFlag] #FeatureFlag PCD is BOOLEAN type, the value is TRUR or FALSE.

#PcdTokenSpaceCGuidName.PcdName|TRUE|BOOLEAN|TokenNumber
#PcdTokenSpaceCGuidName.PcdName|FALSE|BOOLEAN|TokenNumber
[PcdsFixedAtBuild]

#PcdTokenSpaceCGuidName.PcdName|DefaultValue|DataType|TokenNumber
[PcdsPatchableInModule]

#PcdTokenSpaceCGuidName.PcdName|DefaultValue|DataType|TokenNumber
[PcdsDynamic]

#PcdTokenSpaceCGuidName.PcdName|DefaultValue|DataType|TokenNumber
[PcdsDynamicEx]
  #PcdTokenSpaceCGuidName.PcdName|DefaultValue|DataType|TokenNumber
```

Refer to the _EDK II DEC File Specification_ for a detailed description of the
DEC file format_._

### 2.1.4 Package DSC File

Each package usually creates another build description file (DSC). All modules
can be added into DSC to be compiled and verified. DSC has the following
sections:

* Defines

* LibraryClass

* PCD

* Components.

The [Defines] section sets build related information, such as the build output
directory, build target, Guid, and build ARCHs.

The [Components] section lists all modules (Drivers, Application, and Library
Instances) in the platform.

The [LibraryClasses] section specifies the chosen library instance for every
library class, which is consumed by the drivers and applications in the
[Components] section.

The [PCDs] section configures PCD type and value for those PCDs used by the
modules in the [Components] section. If the PCD value is same as the default
value in DEC, and the PCD type has no specific requirement, the PCD may not be
configured in the DSC. Its value and type will be the default setting in DEC.
If all PCDs are not required in the DSC file, the [PCDs] section may be not
created.

**********
**Note:** Only the DSC file for the active platform is used in a build.
**********

The following is a sample DSC file. More modules may be added.

#### 2.1.4.1 Example: Package.dsc

```ini
[Defines]
  PLATFORM_NAME           = PacakgeName
  PLATFORM_GUID           = xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
  PLATFORM_VERSION        = 0.1
  DSC_SPECIFICATION       = 0x00010005
  OUTPUT_DIRECTORY        = Build/PackageName
  SUPPORTED_ARCHITECTURES = IA32|IPF|X64|EBC
  BUILD_TARGETS           = DEBUG|RELEASE
  SKUID_IDENTIFIER        = DEFAULT

[SkuIds] 0|DEFAULT #The entry: 0|DEFAULT is reserved and required.

[LibraryClasses]
  ## More library instances need to be added if more library classes are used
  ## by the components in the following [Components] section.
  ## library class name | library instance INF file path from package
  DebugLib | MdePkg/Library/UefiDebugLibStdErr/UefiDebugLibStdErr.inf
  BaseLib | MdePkg/Library/BaseLib/BaseLib.inf
  BaseMemoryLib | MdePkg/Library/BaseMemoryLib/BaseMemoryLib.inf
  ......

##PCDs sections are not specified.
##All PCDs value are from their Default value in DEC.
##[PcdsFeatureFlag]
##[PcdsFixedAtBuild]
[Components]
  # All libraries, drivers and applications are added here to be compiled
  #
  # Module INF file path are specified from package directory.
  PackageNamePkg/Library/NameOneLib/NameOneLib.inf
  PackageNamePkg/NameOneDxe/NameOneDxe.inf
  PackageNamePkg/NameTwoPei/NameTwoPei.inf
```

A detailed description of the DSC file form is given in the _DSC Specification_.
