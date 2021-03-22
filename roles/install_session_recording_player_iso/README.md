Role Name
=========

Role Name: install_session_recording_player_iso

This role is used to Install the Citrix Session Recording Player from a mounted Citrix CVAD ISO file
located on an SMB share.

Requirements
------------

Role Variables
--------------

The defaults/main.yml contains the following variables and default values:

cvad_session_recording_player_versions:
  1912 LTSR CU2:
    iso_file: Citrix_Virtual_Apps_and_Desktops_7_1912_2000.iso
    product_id: '{1ADB4467-B2BB-4BD8-8721-4A7D848252FC}'
  2012:
    iso_file: Citrix_Virtual_Apps_and_Desktops_7_2012.iso
    product_id: '{E87BD06A-4F55-46EB-86BD-505F86A0BE58}'
  2103:
    iso_file: Citrix_Virtual_Apps_and_Desktops_7_2103.iso
    product_id: '{65863316-03da-4b46-8842-af4c431fd715}'

  smb_location: \\\\file1\\automation

  cvad_session_recording_player_version: 1912 LTSR CU2
  cvad_session_recording_player_iso_location: "{{ smb_location }}\\\\Citrix\\\\Citrix Virtual Apps and Desktops\\\\{{ cvad_session_recording_player_version }}"
  cvad_session_recording_player_iso_file: "{{ cvad_session_recording_player_versions[cvad_session_recording_player_version].iso_file }}"
  cvad_session_recording_player_install_file_location: x86\\Session Recording
  cvad_session_recording_player_install_file: SessionRecordingPlayer.msi
  cvad_session_recording_player_install_arguments: /qn
  cvad_session_recording_player_product_id: "{{ cvad_session_recording_player_versions[cvad_session_recording_player_version].product_id }}"
  cvad_session_recording_player_install_state: present

=======

The cvad_session_recording_player_versions hash table is used to look up the .iso file used, a default set of installation arguments,
the product id of the version being installed.

It's generally not necessary to modify the variables defined in the hash table. There are other variables that can be used to
override the default value and use a desired value instead.

For example:

  cvad_session_recording_player_iso_file: MyTotallyAwesomeISOFile.iso

  can be used to override the value in the hash table:  (2012 in this example)

  cvad_session_recording_player_versions.2012.iso_file: Citrix_Virtual_Apps_and_Desktops_7_2012.iso

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

The cvad_session_recording_player_version variable is used to control which version of the Citrix Session Recording Player will be installed.
This variable is used as the lookup key for the cvad_session_recording_player_versions hash table.

Possible Values are:
  1912 LTSR CU2   (default) : This will install the 1912 LTSR CU2 version of the Citrix Session Recording Player
  2012                      : This will install the 2012 version of the Citrix Session Recording Player
  2103                      : This will install the 2103 version of the Citrix Session Recording Player

The cvad_session_recording_player_iso_location variable aggregates the smb_location with the folder location of the .iso file
including the variable cvad_session_recording_player_version as part of the folder location. The cvad_session_recording_player_iso_location
variable can be customized in the group_vars, host_vars, or even with in the playbook itself.

The cvad_session_recording_player_iso_file variable contains the .iso file that will be mounted and used during the installation process.
By default, the .iso file used is defined in the cvad_session_recording_player_versions hash table using cvad_session_recording_player_version
as the hash key. The cvad_session_recording_player_iso_location variable can be customized in the group_vars, host_vars, or even with in the
playbook itself.

The cvad_session_recording_player_install_file_location and cvad_session_recording_player_install_file variables are used to define the
folder path to the installation executable, once the ISO has been mount, as well as defines the name of the installation
executable. The cvad_session_recording_player_install_file_location and cvad_session_recording_player_install_file variables can be customized
in the group_vars, host_vars, or even with in the playbook itself.

The cvad_session_recording_player_install_arguments variable contains the installation arguments that will be used for the
installation. By default, the cvad_session_recording_player_install_arguments contains the standard silent installation switch /qn
The cvad_session_recording_player_install_arguments variable can be customized in the group_vars, host_vars, or even with in the
the playbook itself.

The cvad_session_recording_player_product_id variable contains the product id relative to the version of the Citrix Session Recording
Player that is being installed. By default, the .iso file used is defined in the cvad_session_recording_player_versions hash table using
cvad_session_recording_player_version as the hash key. The cvad_session_recording_player_product_id variable can be customized
in the group_vars, host_vars, or even with in the playbook itself.

The cvad_session_recording_player_install_state variable controls the installation state for the Citrix Session Recording Player.
This role does not currently support the removal of the Citrix Session Recording Player or any of the pre-requisites and plug-ins
that were installed during the installation process.

Possible Values are:
  present         (default) : This will install the Session Recording Player

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
         - install_session_recording_player_iso

    - hosts: servers
      roles:
         -  role: install_session_recording_player_iso
            vars:
              cvad_session_recording_player_version: 2012

License
-------

GPL-3.0-only

Author Information
------------------

Joe Shonk
