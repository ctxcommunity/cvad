Role Name
=========

Role Name: ctxcommunity.cvad.install_director_iso

This role is used to Install the Citrix Director from a mounted Citrix CVAD ISO file
located on an SMB share.

Requirements
------------

This role should only be used on a Windows Server OS.

A valid certificate in a PFX format is required if the import_and_bind option is used.

Role Variables
--------------

The defaults/main.yml contains the following variables and default values:

  cvad_director_versions:
    1912 LTSR CU2:
      iso_file: Citrix_Virtual_Apps_and_Desktops_7_1912_2000.iso
      install_arguments: /components desktopdirector /noreboot /quiet
      product_id: '{3C377B20-0634-4A6D-9024-0D18FDAD82E9}'
    2012:
      product_id:
      iso_file: Citrix_Virtual_Apps_and_Desktops_7_2012.iso
      install_arguments: /components desktopdirector /noreboot /quiet
      product_id: '{CC29DDE4-C20A-4572-8402-A0EA63B0F208}'

  smb_location: \\\\file1\\automation

  cvad_director_version: 1912 LTSR CU2
  cvad_director_iso_location: "{{ smb_location }}\\\\Citrix\\\\Citrix Virtual Apps and Desktops\\\\{{ cvad_director_version }}"
  cvad_director_iso_file: "{{ cvad_director_versions[cvad_director_version].iso_file }}"
  cvad_director_install_file_location: x64\\XenDesktop Setup
  cvad_director_install_file: XenDesktopServerSetup.exe
  cvad_director_install_arguments: "{{ cvad_director_versions[cvad_director_version].install_arguments }}"
  cvad_director_product_id: "{{ cvad_director_versions[cvad_director_version].product_id }}"
  cvad_director_install_state: present

  # Set to import_and_bind or do_not_import_and_bind
  cvad_director_import_and_bind_pfx_certificate: import_and_bind
  cvad_director_certificate_name: wildcard2021


=======

The cvad_director_versions hash table is used to look up the .iso file used, a default set of installation arguments,
the product id of the version being installed.

It's generally not necessary to modify the variables defined in the hash table. There are other variables that can be used to
override the default value and use a desired value instead.

For example:

  cvad_director_install_arguments: /components desktopdirector,studio /configure_firewall /nosql /noreboot /quiet

  can be used to override the value in the hash table:  (2012 in this example)

  cvad_director_versions.2012.install_arguments: /components desktopdirector /configure_firewall /nosql /noreboot /quiet

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

The cvad_director_version variable is used to control which version of the Citrix Director will be installed.
This variable is used as the lookup key for the cvad_director_versions hash table.

Possible Values are:
  1912 LTSR CU2   (default) : This will install the 1912 LTSR CU2 version of the Citrix Director
  2012                      : This will install the 2012 version of the Citrix Director

The cvad_director_iso_location variable aggregates the smb_location with the folder location of the .iso file
including the variable cvad_director_version as part of the folder location. The cvad_director_iso_location
variable can be customized in the group_vars, host_vars, or even with in the playbook itself.

The cvad_director_iso_file variable contains the .iso file that will be mounted and used during the installation process.
By default, the .iso file used is defined in the cvad_director_versions hash table using cvad_director_version
as the hash key. The cvad_director_iso_location variable can be customized in the group_vars, host_vars, or even with in the
playbook itself.

The cvad_director_install_file_location and cvad_director_install_file variables are used to define the
folder path to the installation executable, once the ISO has been mount, as well as defines the name of the installation
executable. The cvad_director_install_file_location and cvad_director_install_file variables can be customized
in the group_vars, host_vars, or even with in the playbook itself.

The cvad_director_install_arguments variable contains the installation arguments that will be used for the
installation. By default, the cvad_director_install_arguments variable uses the value defined in the
cvad_director_versions hash table using cvad_director_version as the hash key.
The cvad_director_install_arguments variable can be customized in the group_vars, host_vars, or even with in the
the playbook itself.

The cvad_director_product_id variable contains the product id relative to the version of the Director
that is being installed. By default, the .iso file used is defined in the cvad_director_versions hash table using
cvad_director_version as the hash key. The cvad_director_product_id variable can be customized
in the group_vars, host_vars, or even with in the playbook itself.

The cvad_director_install_state variable controls the installation state for the Citrix Director.  This
role does not currently support the removal of the Citrix Director Server, Studio (if installed), or any of the
pre-requisites and plug-ins that were installed during the installation process.

Possible Values are:
  present         (default) : This will install the Director Server

The cvad_director_import_and_bind_pfx_certificate variables determines if a certificate is to imported and bound
to the Director (IIS Default Web Site).

Possible Values are:
  import_and_bind         (default) : This will import and bind the selected certificate to the IIS Default Web Site
  do_not_import_and_bind            : This will skip the import and bind a certificate process

The cvad_director_certificate_name variable defines the certificate name that the Ansible role
ctxcommunity.common.importpfx will use to lookup the certificate information within a vaulted password file. See
the ctxcommunity.common.importpfx README.md for information.

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

ctxcommunity.common.importpfx

Example Playbook
----------------

    - hosts: servers
      roles:
         - ctxcommunity.cvad.install_director_iso

    - hosts: servers
      roles:
        -  role: ctxcommunity.cvad.install_director_iso
           vars:
             cvad_director_version: 2012

License
-------

GPL-3.0-only

Author Information
------------------

Joe Shonk
