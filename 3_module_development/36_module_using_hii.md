<!--- @file
  3.6 Module using HII

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

## 3.6 Module using HII

DXE Modules can publish or update the following resources used in the browser
during the BDS phase:

**Forms**

Describes what type of content needs to be displayed to the user.

**Strings**

The text-based (UCS-2 encoded) representations of the information typically
being referenced by the forms.

**Font/Image**

The contents rendered on a local system.

Please refer to the _UEFI Specification_, Chapter 27, Human Interface
Infrastructure Overview.

### 3.6.1 Forms

#### 3.6.1.1 Create VFR resource file

The VFR file is used to describe form resources, per the example below. A VFR
file is put into a module's directory and referenced in the INF file [Sources]
section, just as with other source code.

```c
Example:
VFR file
#define FORMSET_GUID { 0x9e0c30bc, 0x3f06, 0x4ba6, 0x82, 0x88, 0x9,
0x17, 0x9b, 0x85, 0x5d, 0xbe
}
#define FRONT_PAGE_CLASS 0x0000
#define FRONT_PAGE_SUBCLASS 0x0002
#define FRONT_PAGE_FORM_ID 0x1000
#define FRONT_PAGE_ITEM_ONE 0x0001
#define FRONT_PAGE_ITEM_TWO 0x0002
#define FRONT_PAGE_ITEM_THREE 0x0003
#define FRONT_PAGE_ITEM_FOUR 0x0004
#define FRONT_PAGE_ITEM_FIVE 0x0005
#define FRONT_PAGE_KEY_CONTINUE 0x1000
#define FRONT_PAGE_KEY_LANGUAGE 0x1234
#define FRONT_PAGE_KEY_BOOT_MANAGER 0x1064
#define FRONT_PAGE_KEY_DEVICE_MANAGER 0x8567
#define FRONT_PAGE_KEY_BOOT_MAINTAIN 0x9876
#define LABEL_SELECT_LANGUAGE 0x1000
#define LABEL_TIMEOUT 0x2000
#define LABEL_END 0xffff
formset guid = FORMSET_GUID, title = STRING_TOKEN (
                                       STR_FRONT_PAGE_TITLE),
                                       help = STRING_TOKEN (STR_NULL_STRING),
                                       classguid = EFI_HII_PLATFORM_SETUP_FORMSET_GUID,
                                       form formid = FRONT_PAGE_FORM_ID, title = STRING_TOKEN (STR_FRONT_PAGE_TITLE
                                       );
banner title = STRING_TOKEN (STR_FRONT_PAGE_COMPUTER_MODEL), line 0, align left;
banner title = STRING_TOKEN (STR_FRONT_PAGE_CPU_MODEL), line 1, align left;
banner title = STRING_TOKEN (STR_FRONT_PAGE_CPU_SPEED), line 1, align right;
banner title = STRING_TOKEN (STR_FRONT_PAGE_BIOS_VERSION), line 2, align left;
banner title = STRING_TOKEN (STR_FRONT_PAGE_MEMORY_SIZE), line 2, align right;
goto FRONT_PAGE_ITEM_ONE, prompt = STRING_TOKEN (STR_CONTINUE_PROMPT), help = STRING_TOKEN (STR_CONTINUE_HELP),
                          flags = INTERACTIVE,
                          key = FRONT_PAGE_KEY_CONTINUE;
label LABEL_SELECT_LANGUAGE;
//
// This is where we will dynamically add a OneOf type op-code to select
// Languages from the currently available choices
//
label LABEL_END;
goto FRONT_PAGE_ITEM_THREE, prompt = STRING_TOKEN (STR_BOOT_MANAGER), help = STRING_TOKEN (STR_BOOT_MANAGER_HELP),
                            flags = INTERACTIVE, key = FRONT_PAGE_KEY_BOOT_MANAGER;
goto FRONT_PAGE_ITEM_FOUR, prompt = STRING_TOKEN (STR_DEVICE_MANAGER), help = STRING_TOKEN (STR_DEVICE_MANAGER_HELP),
                           flags = INTERACTIVE, key = FRONT_PAGE_KEY_DEVICE_MANAGER;
goto FRONT_PAGE_ITEM_FIVE, prompt = STRING_TOKEN (STR_BOOT_MAINT_MANAGER),
                           help = STRING_TOKEN (STR_BOOT_MAINT_MANAGER_HELP), flags = INTERACTIVE, key = FRONT_PAGE_KEY_BOOT_MAINTAIN;
endform;
endformset;
```

#### 3.6.1.2 Publish the Form data

When building, VfrCompile will "build" a .vfr file into the IFR binary as a
global array variable in the module image. The name of the global array
variable is `<VfrFileName>` + Bin.

For example, the content of Inventory.vfr in the

MdeModulePkg\Universal\DriverSampleDxe driver is compiled into the global array
variable InventoryBin.

Module developers should use the following code to publish the VFR global array
variable into the HII database:

```c
//
// Create HII driver handle, paramter DriverHandle will hold the
// returned new handle.
// HiiLibCreateHiiDriverHandle defined in UefiHiiLib library class.
//
Status = HiiLibCreateHiiDriverHandle (&DriverHandle);
//
// Prepare HII package list, parameter InventoryBin is the VFR form data
// HiiLibPreparePackageList defined in UefiHiiLib library class
//
PackageList = HiiLibPreparePackageList (
                2,
                &mInventoryGuid,
                InventoryBin,
                DriverSampleStrings
                );
ASSERT (PackageList != NULL);
//
// Create package into HII database via EFI_HII_PROTOCOL-
> NewPackageList
//
Status = gHiiDatabase->NewPackageList (
                         gHiiDatabase,
                         PackageList,
                         DriverHandle,
                         &HiiHandle
                         );
```

When a driver only produces one `formset` in a VFR file, the IFR binary could
be put into a driver's binary as a PE resource section by setting
UEFI_HII_RESOURCE_SECTION to TRUE in the driver's INF file:

```ini
[Defines]
  INF_VERSION    = 0x00010005
  BASE_NAME      = HiiResourcesSample
  FILE_GUID      = D49D2EB0-44D5-4621-9FD6-1A92C9109B99
  MODULE_TYPE    = UEFI_DRIVER
  VERSION_STRING = 1.0
  ENTRY_POINT    = HiiResourcesSampleInit
  UNLOAD_IMAGE   = HiiResourcesSampleUnload
  #
  # This flag specifies whether HII resource section is generated into PE image.
  #
  UEFI_HII_RESOURCE_SECTION = TRUE
```

Module developers should use the following code to publish HII package data
into the HII database:

```c
//
// Retrieve HII package list from ImageHandle
//
Status = gBS->OpenProtocol (
                ImageHandle,
                &gEfiHiiPackageListProtocolGuid,
                (VOID **) &PackageList,
                ImageHandle,
                NULL,
                EFI_OPEN_PROTOCOL_GET_PROTOCOL
                );
if (EFI_ERROR (Status))
{
  return Status;
}
//
// Publish sample Fromset
//
Status = gBS->InstallProtocolInterface (
                &mDriverHandle,
                &gEfiDevicePathProtocolGuid,
                EFI_NATIVE_INTERFACE,
                &mHiiVendorDevicePath
                );
if (EFI_ERROR (Status))
{
  return Status;
}
//
// Publish HII package list to HII Database.
//
Status = gHiiDatabase->NewPackageList (
                         gHiiDatabase,
                         PackageList,
                         mDriverHandle,
                         &mHiiHandle
                         );
if (EFI_ERROR (Status))
{
  return Status;
}
```

### 3.6.2 Using Unicode Strings
#### 3.6.2.1 Create .uni file

The Unicode strings are put into the .uni file and referenced in the module's
INF [Sources] section like others C files. The .uni file is encoding UCS-2 with
a 0xFFFE BOM header. For example:
```
/=# 

#langdef en-US "English" 

#langdef fr-FR "Francais" 

#string STR_INV_FORM_SET_TITLE #language en-US "Network 

Controller Information" 

#language fr-FR "Mi motor Español de arreglo"

#string STR_INV_FORM_SET_HELP #language en-US "The ABC Network Controller
version information, which includes Firmware versions as well as supported
characteristics"

#language fr-FR "The ABC Network Controller version information, which
includes Firmware versions as well as supported characteristics"

#string STR_INV_FORM1_TITLE #language en-US "ABC Network

Controller Version Data"

#language fr-FR "Mi Primero

Arreglo Página"

#string STR_INV_VERSION_TEXT #language en-US "Firmware

Revision Date: 02/03/2002"

#language fr-FR "Firmware

Revision Date: 02/03/2002"

#string STR_INV_VERSION_HELP #language en-US "The date of the revision of the
Firmware being used."

#language fr-FR "The date of the revision of the Firmware being used."
```

#### 3.6.2.2 Publish the Unicode String file


```c
The file content in .uni file will be parsed

and compiled by build tool to a binary string package array for a module. The name of binary array is constructed as

<ModuleName> { + "Strings". For example, the inventorystring.uni defined in MdeModulePkg\Universal\DriverSampleDxe is compiled to binary array: extern UINT8 DriverSampleStrings[]; }
```

Module developers should use the following codes to publish the strings array
variable into the HII database:

```c
//
// Create HII driver handle, paramter DriverHandle will hold the
// returned new handle.
// HiiLibCreateHiiDriverHandle defined in UefiHiiLib library class.
//
Status = HiiLibCreateHiiDriverHandle (&DriverHandle);
//
// Prepare HII package list, parameter DriverSampleStrings is the
// strings binary data.
// HiiLibPreparePackageList defined in UefiHiiLib library class
//
PackageList = HiiLibPreparePackageList (
                2,
                &mFormSetGuid,
                DriverSampleStrings,
                VfrBin
                );
if (PackageList == NULL)
{
  return EFI_OUT_OF_RESOURCES;
}
//
// Create package into HII database via EFI_HII_PROTOCOL-
> NewPackageList
//
Status = HiiDatabase->NewPackageList (
                        HiiDatabase,
                        PackageList,
                        DriverHandle[0],
                        &HiiHandle[0]
                        );
```

As with other types HII resources, if a module publishs the HII data into a PE
resource section, UEFI_HII_RESOURCE_SECTION is set to TRUE in the module's INF
file and the following code is used:

```c
//
// Retrieve HII package list from ImageHandle
//
Status = gBS->OpenProtocol (
                ImageHandle,
                &gEfiHiiPackageListProtocolGuid,
                (VOID **) &PackageList,
                ImageHandle,
                NULL,
                EFI_OPEN_PROTOCOL_GET_PROTOCOL
                );
if (EFI_ERROR (Status))
{
  return Status;
}
//
// Publish sample Fromset
//
Status = gBS->InstallProtocolInterface (
                &mDriverHandle,
                &gEfiDevicePathProtocolGuid,
                EFI_NATIVE_INTERFACE,
                &mHiiVendorDevicePath
                );
if (EFI_ERROR (Status))
{
  return Status;
}
//
// Publish HII package list to HII Database.
//
Status = gHiiDatabase->NewPackageList (
                         gHiiDatabase,
                         PackageList,
                         mDriverHandle,
                         &mHiiHandle
                         );
if (EFI_ERROR (Status))
{
  return Status;
}
```
