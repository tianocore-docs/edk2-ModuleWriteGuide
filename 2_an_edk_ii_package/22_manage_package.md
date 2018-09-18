<!--- @file
  2.2 Manage Package

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

## 2.2 Manage Package

### 2.2.1 Create Package

When current packages do not satisfy a requirement, or the original code base
is split into EDK II packages, new packages need to be created. Following are
the recommended rules for defining the new package:

* All modules related to the same functionality should go in the same package.
  For example, different packages should be created for different chipsets.

* Generic modules shared between different platforms should be in another
  package. For example, the MdePkg and MdeModulePkg are shared.

* Modules should go in packages according to their release requirements. If
  modules are released only to specific customers, they should go in specific
  packages.

**********
**Note:** There is no limitation for source files in a new package. Even if
only one file is in a package, this package will be valid.
**********

According to the rules given immediately above, the EDK II project provides
several packages for user reference.

When a new package is added, the following steps are used to create it.

* Give the meaningful package name as the package directory name and create a
  package directory, such PackageNamePkg.

* Create package DEC and DSC files in the package root directory to describe
  this package.

* Create package sub-directories to contain the different source files.

### 2.2.2 Using a Package

A package provides public header files, library classes, PCDs and modules,
which are required to develop other modules and platforms.

A module is dependent on the package that it resides in and may be dependent on
other packages.

A platform is made from the modules contained in its own package, and from
other packages.

The EDK II package is the basic development unit. It can be used to configure
the development workspace. According to development requirements, the workspace
can integrate different packages from the EDK II project and other sources. To
develop a module or a platform, their dependent packages need to be integrated
into the workspace. For example, to develop UEFI and PI driver, the MdePkg,
which contains all UEFI/PI definitions, is required in the workspace.

The following show how to develop modules and platforms based on packages.

* Each package DEC file and Include directory lists package public header
  files, library classes and PCDs. When a new module is developed, it can
  include information from the DECs of all packages in current workspaces If it
  needs information from a package that is not in the current workspace, this
  package needs to be added.

* Each package DSC file lists all modules provided by this package. The
  developer can search the DSCs of all packages in the current workspace to
  obtain the required modules (and move their information into the platform DSC
  file). Then it specifies those modules in the platform DSC and FDF file. If a
  new platform still requires the modules from a packages that are not in the
  current workspace, this package needs to be added.

### 2.2.3 Updating a Package

The package DEC and DSC files describe the package capability, which should be
created according to source files of this package. If source files are changed,
removed, or added, the package DEC and DSC file must be updated to match their
change.

All changes to source code that affect the DEC and DSC file are introduced one
by one.

#### 2.2.3.1 Updating Package Include Directories

When a package Include directory is changed, added, or removed, the [Includes]
a section of the DEC must be updated.

##### 2.2.3.1.1 Example: Include section of Package.dec

```ini
[Includes]
  Include # Package Include path
  LocalInclude # Add new include path
```

#### 2.2.3.2 Updating Guids/Ppis/Protocols

When a Guid value or Guid global CName defined in the package public Guid
header file changes, the [Guids] section of the DEC must be updated to the new
Guid value or Guid CName. If a public Guid header file is removed, the Guid
defined in this file must be removed from the [Guids] section of the DEC. If a
new guid header file is added in the package public include directory, the new
declared Guid and its value must be added to the [Guids] section of the DEC.
Like the Guid header file, any change to Guid values defined in the Ppi and
Protocol header files also requires the [Ppis] or the [Protocols] section to be
updated.

##### 2.2.3.2.1 Example: Guid section of Package.dec

```ini
[Guids]
  #gGuidCName = {00000000,0000,0000,{00,00,00,00,00,00,00,00}}
  #updated to
  gNewGuidCName = {00000000,0000,0000,{00,00,00,00,00,00,00,01}}
```

#### 2.2.3.3 Updating Library Classes

When the library class name is changed, the library class header file name
needs to update the [LibraryClasses] section of the DEC to map the new library
class name to the (new?) header file. The change to the library class name will
also require the [LibraryClasses] section (of the DSC to be updated) to map the
new library class name and the library instance. When a new library class is
introduced, its name and its header file will be specified in the DEC
[LibraryClasses] section.

##### 2.2.3.3.1 Example: LibraryClasses section of Package.dsc

```ini
[LibraryClasses]
  #OneClassLib|Include/Library/OneClassLib.inf updated to
  BaseMemoryLib|MdePkg/Library/BaseMemoryLib/BaseMemoryLib.inf
```

#### 2.2.3.4 Updating PCDs

PCDs are declared in the package DEC and are not related to any header file.
However, module source files use them. If a PCD does not exist in any module,
its declaration should be removed from the DEC file. The setting for this PCD
in DSC file should also be removed.

When a module requires a new PCD, it needs to define this PCD in the DEC file
for the package where the module is located. Then the DEC file will specify the
PCD type and default value.

##### 2.2.3.4.1 Example: Package.dec

```ini
[PcdsFixedAtBuild]
  #Add new FixedAtBuild PCD
  #PcdTokenSpaceCGuidName.PcdName|DefaultValue|DataType|TokenNumber
  gEfiMdeModulePkgTokenSpaceGuid.PcdHelloWorldTimes|1|UINT32|0x40000005
```

#### 2.2.3.5 Updating Modules

Changes to modules (Library instance, drivers and applications) cause the
dependent header file, library class and PCDs be modified, which requires the
DSC file to be updated.

If a module INF file name is changed, the DSC files that refer to this module
are updated to new file name. If a module is completely removed, it will not be
compiled any more, and is removed from the package DSC. When a new module is
added to a package, it should be added to the package DSC to be compiled and
verified.

##### 2.2.3.5.1 Example: Package.dsc

```ini
[Components] #Module INF file path are specified from package directory.
  # PackageNamePkg/NameTwoPei/NameTwoDxe.inf
  # updated to
  MdeModulePkg/Application/HelloWorld/HelloWorld.inf
```
