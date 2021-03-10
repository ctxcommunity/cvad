Role Name
=========

Role Name: ctxcommunity.cvad.install_vda_iso

This role is used to install the Citrix VDA using an .ISO file mounted from an SMB share.

Requirements
------------

For installation on a Windows Server OS, the Windows Server RDS Server Role needs to be first installed.

The Windows .NET 4.8 Framework is a pre-requisite for all OSes.

WVD MultiUser Win10 has not been tested yet. It is on the roadmap.
Single User Server VDI is not supported yet. It is a different installation process that will be added
if there is interest in this deployment method.

Role Variables
--------------

https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/install-configure/install-vdas.html

The defaults/main.yml contains the following variables and default values:

cvad_vda_versions:
  1912 LTSR CU2:
    product_id: '{89BD0C39-1C8E-44D2-9923-1167EAB7BEEB}'
    iso_file: Citrix_Virtual_Apps_and_Desktops_7_1912_2000.iso
    install_arguments: /enable_hdx_ports /enable_hdx_udp_ports /disableexperiencemetrics /enable_real_time_transport /enable_remote_assistance /noreboot /quiet
  2012:
    product_id: '{C344C994-94D2-45EC-AD93-0EBBA1E9D5DD}'
    iso_file: Citrix_Virtual_Apps_and_Desktops_7_2012.iso
    install_arguments: /enable_hdx_ports /enable_hdx_udp_ports /disableexperiencemetrics /enable_real_time_transport /enable_remote_assistance /noreboot /quiet

https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/install-command.html
cvad_vda_provisioning_tools:
  mcs:
    install_arguments: /install_mcsio_driver /mastermcsimage
  pvs:
    install_arguments: /virtualmachine
  remote pc:
    install_arguments: /remotepc
  none:
    install_arguments: ~

cvad_vda_components:
    vda only:
      install_arguments: vda
    vda with workspace app:
      install_arguments: vda,plugins

# Options are
#   1912 LTSR CU2
#   2012
cvad_vda_version: 2012
# Options are
#   pvs
#   mcs
#   remotepc
#   none
cvad_vda_provisioning_tool: mcs
# Options are
#   vda only
#   vda with workspace app
cvad_vda_component: vda only

Can add WEM Agent Install, for example, using the cvad_vda_includeadditional_arguments
Note: The WEM Agent is not included with the 1912 LTSR CU2 version of the VDA installer

cvad_vda_includeadditional_arguments: ~
cvad_vda_exclude_arguments: ~

smb_location: \\\\file1\\automation
cvad_vda_iso_location: "{{ smb_location }}\\\\Citrix\\\\Citrix Virtual Apps and Desktops\\\\{{ cvad_vda_version }}"
cvad_vda_iso_file: "{{ cvad_vda_versions[cvad_vda_version].iso_file }}"
cvad_vda_install_file_location: x64\\XenDesktop Setup
cvad_vda_install_file: XenDesktopVDASetup.exe
cvad_vda_install_arguments: "/components {{ cvad_vda_components[cvad_vda_component].install_arguments }} {{ cvad_vda_versions[cvad_vda_version].install_arguments }} {{ cvad_vda_includeadditional_arguments }} {{ cvad_vda_exclude_arguments }} {{ cvad_vda_provisioning_tools[cvad_vda_provisioning_tool].install_arguments }}"
cvad_vda_product_id: "{{ cvad_vda_versions[cvad_vda_version].product_id }}"
cvad_vda_install_state: present


=======

An SMB location must defined with the Ansible Become User having a minimum of Read-Only access to the Share and folder structure.
The smb_location variable can be defined in the group_vars, host_vars, or even with in the playbook itself.

Sample SMB File and Folder Structure:
  \\\\file1\\automation\\
      Citrix\\
          Citrix Virtual Apps and Desktops\\
              1912 LTSR CU2\\
                  Citrix_Virtual_Apps_and_Desktops_7_1912_2000.iso
              2012\\
                  Citrix_Virtual_Apps_and_Desktops_7_2012.iso

The cvad_vda_versions hash table is used to look up the .iso file used, a default set of installation arguments,
the product id of the version being installed.

It's generally not necessary to modify the variables defined in the hash table. There are other variables that can be used to
override the default value and use a desired value instead.

For example:

  cvad_vda_install_arguments: install_arguments: /enable_hdx_ports /enable_hdx_udp_ports /disableexperiencemetrics /enable_real_time_transport /enable_remote_assistance /noreboot /quiet

  can be used to override the value in the hash table:  (2012 in this example)

  cvad_vda_versions.2012.install_arguments: install_arguments: /enable_hdx_ports /enable_hdx_udp_ports /enable_real_time_transport /enable_remote_assistance /noreboot /quiet

The cvad_vda_provisioning_tools hash table is used to look up augmented installation arguments that are specific to the provisioning type
that the Citrix VDA will use. The cvad_vda_provisioning_tool is the variable that used as the lookup key.

The cvad_vda_components hash table is used to look up augmented installation arguments that are specific to the provisioning type
that the Citrix VDA will use. The cvad_vda_component is the variable that used as the lookup key.

The cvad_vda_version variable is used to control which version of the Citrix VDA will be installed.
This variable is used as the lookup key for the cvad_vda_versions hash table.

Possible Values are:
  1912 LTSR CU2   (default) : This will install the 1912 LTSR CU2 version of the Citrix VDA
  2012                      : This will install the 2012 version of the Citrix VDA

https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/install-command.html
The cvad_vda_provisioning_tool variable is used to control which version of the Citrix VDA will be installed.
This variable is used as the lookup key for the cvad_vda_provisioning_tools hash table.

Possible Values are:
  msc             (default) : This will install the 1912 LTSR CU2 version of the Citrix VDA
  pvs                       : This will install the 2012 version of the Citrix VDA
  remotepc                  : This will install the 2012 version of the Citrix VDA
  none                      : This will install the 2012 version of the Citrix VDA

The cvad_vda_component variable is used to control which version of the Citrix VDA will be installed.
This variable is used as the lookup key for the cvad_vda_components hash table.

Possible Values are:
  vda only                (default) : This will install the Citrix VDA
  vda with workspace app            : This will install the Citrix VDA with the WorkSpace App


The cvad_vda_includeadditional_arguments and cvad_vda_exclude_arguments variables can be used to add and/or exclude
additional features that are available during the Citrix VDA installation. The Citrix WEM Agent is one such
example. See the above Citrix Docs for additional information.

Note: The WEM Agent is not included with the 1912 LTSR CU2 version of the VDA installer

The cvad_vda_iso_location variable aggregates the smb_location with the folder location of the .iso file
including the variable cvad_vda_version as part of the folder location. The cvad_vda_iso_location
variable can be customized in the group_vars, host_vars, or even with in the playbook itself.

The cvad_vda_iso_file variable contains the .iso file that will be mounted and used during the installation process.
By default, the .iso file used is defined in the cvad_vda_versions hash table using cvad_vda_version
as the hash key. The cvad_vda_iso_location variable can be customized in the group_vars, host_vars, or even with in the
playbook itself.

The cvad_vda_install_file_location and cvad_vda_install_file variables are used to define the
folder path to the installation executable, once the ISO has been mount, as well as defines the name of the installation
executable. The cvad_vda_install_file_location and cvad_vda_install_file variables can be customized
in the group_vars, host_vars, or even with in the playbook itself.

The cvad_vda_install_arguments variable contains the installation arguments that will be used for the
installation. By default, the cvad_vda_install_arguments variable uses the value defined in the
cvad_vda_versions hash table using cvad_vda_version as the hash key.
The cvad_vda_install_arguments variable can be customized in the group_vars, host_vars, or even with in the
the playbook itself.

The cvad_vda_product_id variable contains the product id relative to the version of the Citrix VDA
that is being installed. By default, the .iso file used is defined in the cvad_vda_versions hash table using
cvad_vda_version as the hash key. The cvad_vda_product_id variable can be customized
in the group_vars, host_vars, or even with in the playbook itself.

The cvad_vda_install_state variable controls the installation state for the Citrix VDA.  This
role does not currently support the removal of the Citrix VDA Server, Studio (if installed), or any of the
pre-requisites and plug-ins that were installed during the installation process.

Possible Values are:
  present         (default) : This will install the Citrix VDA

While the defaults/main.yml file can be modified directly, future updates will
overwrite the defaults/main.yml file.  In addition, many of the roles use the same
variables and could benefit from the variable being set at a high precedence.
The variable smb_location is one such example.

To override the default variable values the best place(s) to accomplish this is
to set the new values in one or more of the following locations:
  host_vars/{hostname.mycompany.com}.yml
  group_vars/{group}.yml
  group_vars/all.yml
  playbook.yml (Best option if you need to import multiple certificates)

Dependencies
------------

Decided to remove dependency as not all machines with the VDA are RDS servers
  Windows 10, for example

The ctxcommunity.windows.rds_server role is still required for mullti-user
server installations and should be installed before attempting to install
the Citrix VDA role

The ctxcommunity.windows.dotnet_48_framework_runtime role is required for the Citrix 2012 VDA
version of the installation. The ctxcommunity.windows.dotnet_48_framework_runtime uses a handler and executes
a flush_handlers at the end of the tasks sequence. The flush_handlers task does not support
'when' conditionals.

Example Playbook
----------------

    - hosts: servers
      roles:
         - ctxcommunity.cvad.install_vda_iso

License
-------

GPL-3.0-only

Author Information
------------------

Joe Shonk
