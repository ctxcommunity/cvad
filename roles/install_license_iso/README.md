Role Name
=========

Role Name: ctxcommunity.cvad.install_license_iso

This role is used to Install the Citrix License Server from a mounted Citrix CVAD ISO file
located on an SMB share.

Requirements
------------

This role should only be used on a Windows Server OS.

Role Variables
--------------

The defaults/main.yml contains the following variables and default values:

  cvad_license_versions:
    1912 LTSR CU2:
      iso_file: Citrix_Virtual_Apps_and_Desktops_7_1912_2000.iso
      install_arguments: /components licenseserver /noreboot /quiet
      product_id: '{386D0715-F44A-40E9-BD10-D81139BBDB7F}'
    1912 LTSR CU3:
      iso_file: Citrix_Virtual_Apps_and_Desktops_7_1912_3000.iso
      install_arguments: /components licenseserver /noreboot /quiet
      product_id: '{17086151-418C-40D7-B25B-1E5AE2F76DD0}'
    2012:
      iso_file: Citrix_Virtual_Apps_and_Desktops_7_2012.iso
      install_arguments: /components licenseserver /noreboot /quiet
      product_id: '{ECF1488A-6F66-4FA4-B2A8-7FBD5BB86A58}'
    2103:
      iso_file: Citrix_Virtual_Apps_and_Desktops_7_2103.iso
      install_arguments: /components licenseserver /noreboot /quiet
      product_id: '{17086151-418C-40D7-B25B-1E5AE2F76DD0}'


  smb_location: \\\\file1\\automation

  cvad_license_version: 1912 LTSR CU3
  cvad_license_iso_location: "{{ smb_location }}\\\\Citrix\\\\Citrix Virtual Apps and Desktops\\\\{{ cvad_license_version }}"
  cvad_license_iso_file: "{{ cvad_license_versions[cvad_license_version].iso_file }}"
  cvad_license_install_file_location: x64\\XenDesktop Setup
  cvad_license_install_file: XenDesktopServerSetup.exe
  cvad_license_install_arguments: "{{ cvad_license_versions[cvad_license_version].install_arguments }}"
  cvad_license_product_id: "{{ cvad_license_versions[cvad_license_version].product_id }}"
  cvad_license_install_state: present

=======

The cvad_license_versions hash table is used to look up the .iso file used, a default set of installation arguments,
the product id of the version being installed.

It's generally not necessary to modify the variables defined in the hash table. There are other variables that can be used to
override the default value and use a desired value instead.

For example:

  cvad_license_install_arguments: /components licenseserver,studio /configure_firewall /nosql /noreboot /quiet

  can be used to override the value in the hash table:  (2012 in this example)

  cvad_license_versions.2012.install_arguments: /components licenseserver /configure_firewall /nosql /noreboot /quiet

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

The cvad_license_version variable is used to control which version of the Citrix License will be installed.
This variable is used as the lookup key for the cvad_license_versions hash table.

Possible Values are:
  1912 LTSR CU2             : This will install the 1912 LTSR CU2 version of the Citrix License Server
  1912 LTSR CU2   (default) : This will install the 1912 LTSR CU3 version of the Citrix License Server
  2012                      : This will install the 2012 version of the Citrix License Server
  2103                      : This will install the 2103 version of the Citrix License Server

The cvad_license_iso_location variable aggregates the smb_location with the folder location of the .iso file
including the variable cvad_license_version as part of the folder location. The cvad_license_iso_location
variable can be customized in the group_vars, host_vars, or even with in the playbook itself.

The cvad_license_iso_file variable contains the .iso file that will be mounted and used during the installation process.
By default, the .iso file used is defined in the cvad_license_versions hash table using cvad_license_version
as the hash key. The cvad_license_iso_location variable can be customized in the group_vars, host_vars, or even with in the
playbook itself.

The cvad_license_install_file_location and cvad_license_install_file variables are used to define the
folder path to the installation executable, once the ISO has been mount, as well as defines the name of the installation
executable. The cvad_license_install_file_location and cvad_license_install_file variables can be customized
in the group_vars, host_vars, or even with in the playbook itself.

The cvad_license_install_arguments variable contains the installation arguments that will be used for the
installation. By default, the cvad_license_install_arguments variable uses the value defined in the
cvad_license_versions hash table using cvad_license_version as the hash key.
The cvad_license_install_arguments variable can be customized in the group_vars, host_vars, or even with in the
the playbook itself.

The cvad_license_product_id variable contains the product id relative to the version of the License
that is being installed. By default, the .iso file used is defined in the cvad_license_versions hash table using
cvad_license_version as the hash key. The cvad_license_product_id variable can be customized
in the group_vars, host_vars, or even with in the playbook itself.

The cvad_license_install_state variable controls the installation state for the Citrix License.  This
role does not currently support the removal of the Citrix License Server, Studio (if installed), or any of the
pre-requisites and plug-ins that were installed during the installation process.

Possible Values are:
  present         (default) : This will install the License Server

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

No dependencies.

Example Playbook
----------------

    - hosts: servers
      roles:
         - ctxcommunity.cvad.install_license_iso

    - hosts: servers
      roles:
         -  role: ctxcommunity.cvad.install_license_iso
            vars:
              cvad_license_version: 2012

License
-------

GPL-3.0-only

Author Information
------------------

Joe Shonk
