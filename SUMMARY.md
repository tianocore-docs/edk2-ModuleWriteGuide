<!--- @file
  Summary

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

# Summary

* [EDK II Module Writer's Guide](README.md#edk-ii-module-writers-guide)
* [Tables](TABLES.md#tables)
* [Figures](FIGURES.md#figures)
* [1 The Basics of EDK II](1_the_basics_of_edk_ii/README.md#1-the-basics-of-edk-ii)
  * [1.1 Overview](1_the_basics_of_edk_ii/11_overview.md#11-overview)
  * [1.2 Related References](1_the_basics_of_edk_ii/12_related_references.md#12-related-references)
  * [1.3 Terms](1_the_basics_of_edk_ii/13_terms.md#13-terms)
  * [1.4 Target Audience](1_the_basics_of_edk_ii/14_target_audience.md#14-target-audience)
* [2 An EDK II Package](2_an_edk_ii_package/README.md#2-an-edk-ii-package)
  * [2.1 Introduction](2_an_edk_ii_package/21_introduction.md#21-introduction)
  * [2.2 Manage Package](2_an_edk_ii_package/22_manage_package.md#22-manage-package)
* [3 Module Development](3_module_development/README.md#3-module-development) 
  * [3.1 What is an EDK II module?](3_module_development/31_what_is_an_edk_ii_module.md#31-what-is-an-edk-ii-module)
  * [3.2 Creating a Module](3_module_development/32_creating_a_module.md#32-creating-a-module)
  * [3.3 Additional Steps for Library Instances](3_module_development/33_additional_steps_for_library_instances.md#33-additional-steps-for-library-instances)
  * [3.4 Additional Steps for Driver](3_module_development/34_additional_steps_for_driver.md#34-additional-steps-for-driver)
  * [3.5 EDK II Common Library Class](3_module_development/35_edk_ii_common_library_class.md#35-edk-ii-common-library-class)
  * [3.6 Module using HII](3_module_development/36_module_using_hii.md#36-module-using-hii)
  * [3.7 Building the module](3_module_development/37_building_the_module.md#37-building-the-module)
  * [3.8 Debugging a Module](3_module_development/38_debugging_a_module.md#38-debugging-a-module)
* [4 UEFI Applications](4_uefi_applications/README.md#4-uefi-applications)
  * [4.1 Begin with INF file](4_uefi_applications/41_begin_with_inf_file.md#41-begin-with-inf-file)
  * [4.2 Write UEFI Application Entry Point](4_uefi_applications/42_write_uefi_application_entry_point.md#42-write-uefi-application-entry-point)
  * [4.3 Get Service Tables](4_uefi_applications/43_get_service_tables.md#43-get-service-tables)
  * [4.4 Communicating with a UEFI driver](4_uefi_applications/44_communicating_with_a_uefi_driver.md#44-communicating-with-a-uefi-driver)
* [5 UEFI Drivers](5_uefi_drivers/README.md#5-uefi-drivers)
  * [5.1 Begin With INF File](5_uefi_drivers/51_begin_with_inf_file.md#51-begin-with-inf-file)
  * [5.2 Write the UEFI Driver entry point](5_uefi_drivers/52_write_the_uefi_driver_entry_point.md#52-write-the-uefi-driver-entry-point)
  * [5.3 Get Service Tables](5_uefi_drivers/53_get_service_tables.md#53-get-service-tables)
  * [5.4 Communication between UEFI Drivers](5_uefi_drivers/54_communication_between_uefi_drivers.md#54-communication-between-uefi-drivers)
* [6 SEC Module](6_sec_module/README.md#6-sec-module)
  * [6.1 Beginning to Write the INF File](6_sec_module/61_beginning_to_write_the_inf_file.md#61-beginning-to-write-the-inf-file)
  * [6.2 Setup Pre-Memory Environment](6_sec_module/62_setup_pre-memory_environment.md#62-setup-pre-memory-environment)
  * [6.3 Prepare for Data PEI Foundation](6_sec_module/63_prepare_for_data_pei_foundation.md#63-prepare-for-data-pei-foundation)
* [7 Pre-EFI Initialization Modules](7_pre-efi_initialization_modules/README.md#7-pre-efi-initialization-modules)
  * [7.1 Introduction](7_pre-efi_initialization_modules/71_introduction.md#71-introduction)
  * [7.2 Beginning to Write a PEIM INF File](7_pre-efi_initialization_modules/72_beginning_to_write_a_peim_inf_file.md#72-beginning-to-write-a-peim-inf-file)
  * [7.3 Defining a PEIM's entry point](7_pre-efi_initialization_modules/73_defining_a_peims_entry_point.md#73-defining-a-peims-entry-point)
  * [7.4 Get Pei Services](7_pre-efi_initialization_modules/74_get_pei_services.md#74-get-pei-services)
  * [7.5 Communicate between PEIM Modules](7_pre-efi_initialization_modules/75_communicate_between_peim_modules.md#75-communicate-between-peim-modules)
  * [7.6 Communicate with DXE Modules](7_pre-efi_initialization_modules/76_communicate_with_dxe_modules.md#76-communicate-with-dxe-modules)
  * [7.7 Boot Mode](7_pre-efi_initialization_modules/77_boot_mode.md#77-boot-mode)
  * [7.8 Execution in Place PEIMs](7_pre-efi_initialization_modules/78_execution_in_place_peims.md#78-execution-in-place-peims)
  * [7.9 Dependency for PEIMs](7_pre-efi_initialization_modules/79_dependency_for_peims.md#79-dependency-for-peims)
* [8 DXE Drivers: non-UEFI drivers](8_dxe_drivers_non-uefi_drivers/README.md#8-dxe-drivers-non-uefi-drivers)
  * [8.1 Beginning with INF File](8_dxe_drivers_non-uefi_drivers/81_beginning_with_inf_file.md#81-beginning-with-inf-file)
  * [8.2 Write DXE Driver Entry Point](8_dxe_drivers_non-uefi_drivers/82_write_dxe_driver_entry_point.md#82-write-dxe-driver-entry-point)
  * [8.3 Obtaining Services Tables](8_dxe_drivers_non-uefi_drivers/83_obtaining_services_tables.md#83-obtaining-services-tables)
  * [8.4 Communication between DXE Drivers](8_dxe_drivers_non-uefi_drivers/84_communication_between_dxe_drivers.md#84-communication-between-dxe-drivers)
  * [8.5 Communication with PEIMs](8_dxe_drivers_non-uefi_drivers/85_communication_with_peims.md#85-communication-with-peims)
  * [8.6 Dependency Expressions](8_dxe_drivers_non-uefi_drivers/86_dependency_expressions.md#86-dependency-expressions)
  * [8.7 Handler for EVT_SIGNAL_EXIT_BOOT_SERVICES](8_dxe_drivers_non-uefi_drivers/87_handler_for_evtsignal_exit_boot_services.md#87-handler-for-evt_signal_exit_boot_services)
  * [8.8 DXE Runtime Driver](8_dxe_drivers_non-uefi_drivers/88_dxe_runtime_driver.md#88-dxe-runtime-driver)
  * [8.9 DXE SAL Driver](8_dxe_drivers_non-uefi_drivers/89_dxe_sal_driver.md#89-dxe-sal-driver)
  * [8.10 DXE SMM Driver](8_dxe_drivers_non-uefi_drivers/810_dxe_smm_driver.md#810-dxe-smm-driver)
* [Appendix A Dynamic PCD](appendix_a_dynamic_pcd.md#appendix-a-dynamic-pcd)
