Role Name
=========

Role Name: ctxcommunity.cvad.install_session_recording_agent_iso

This role is used to Install the Citrix Recording Agent from a mounted Citrix CVAD ISO file
located on an SMB share.

Requirements
------------

MSMQ

Role Variables
--------------

The defaults/main.yml contains the following variables and default values:

cvad_session_recording_agent_versions:
  1912 LTSR CU2:
    iso_file: Citrix_Virtual_Apps_and_Desktops_7_1912_2000.iso
    product_id: '{5C31D558-520E-4060-9A26-F4E87F52E822}'
  2012:
    iso_file: Citrix_Virtual_Apps_and_Desktops_7_2012.iso
    product_id: '{9B3DEC67-CE4B-4170-8EF7-1D29DAFEE151}'

cvad_session_recording_agent_servername: myservername.mycompany.com
cvad_session_recording_agent_broker_protocol: HTTPS
cvad_session_recording_agent_broker_port: 443

smb_location: \\\\file1\\\automation

cvad_session_recording_agent_version: 1912 LTSR CU2
cvad_session_recording_agent_iso_location: "{{ smb_location }}\\\\Citrix\\\\Citrix Virtual Apps and Desktops\\\\{{ cvad_session_recording_agent_version }}"
cvad_session_recording_agent_iso_file: "{{ cvad_session_recording_agent_versions[cvad_session_recording_agent_version].iso_file }}"
cvad_session_recording_agent_install_file_location: x64\\Session Recording
cvad_session_recording_agent_install_file: SessionRecordingAgentx64.msi
cvad_session_recording_agent_install_arguments: "/qn SESSIONRECORDINGSERVERNAME={{ cvad_session_recording_agent_servername }} SESSIONRECORDINGBROKERPROTOCOL={{ cvad_session_recording_agent_broker_protocol }} SESSIONRECORDINGBROKERPORT={{ cvad_session_recording_agent_broker_port }}"
cvad_session_recording_agent_product_id: "{{ cvad_session_recording_agent_versions[cvad_session_recording_agent_version].product_id }}"
cvad_session_recording_agent_install_state: present

=======

The cvad_session_recording_agent_versions hash table is used to look up the .iso file used, a default set of installation arguments,
the product id of the version being installed.

It's generally not necessary to modify the variables defined in the hash table. There are other variables that can be used to
override the default value and use a desired value instead.

For example:

  The cvad_session_recording_agent_iso_file: MyTotallyAwesomeISOFile.iso

  can be used to override the value in the hash table:  (2012 in this example)

  The cvad_session_recording_agent_versions.2012.iso_file: Citrix_Virtual_Apps_and_Desktops_7_2012.iso

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

The cvad_session_recording_agent_version variable is used to control which version of the Citrix Recording Agent will be installed.
This variable is used as the lookup key for the The cvad_session_recording_agent_versions hash table.

Possible Values are:
  1912 LTSR CU2   (default) : This will install the 1912 LTSR CU2 version of the Citrix Session Recording Agent
  2012                      : This will install the 2012 version of the Citrix Session Recording Agent

????????

The cvad_session_recording_agent_iso_location variable aggregates the smb_location with the folder location of the .iso file
including the variable cvad_session_recording_agent_version as part of the folder location. The cvad_session_recording_agent_iso_location
variable can be customized in the group_vars, host_vars, or even with in the playbook itself.

The cvad_session_recording_agent_iso_file variable contains the .iso file that will be mounted and used during the installation process.
By default, the .iso file used is defined in the cvad_session_recording_agent_versions hash table using cvad_session_recording_agent_version
as the hash key. The cvad_session_recording_agent_iso_location variable can be customized in the group_vars, host_vars, or even with in the
playbook itself.

The cvad_session_recording_agent_install_file_location and cvad_session_recording_agent_install_file variables are used to define the
folder path to the installation executable, once the ISO has been mount, as well as defines the name of the installation
executable. The cvad_session_recording_agent_install_file_location and cvad_session_recording_agent_install_file variables can be customized
in the group_vars, host_vars, or even with in the playbook itself.

The cvad_session_recording_agent_servername variable defines the location of the Session Recording Server the agent will connect to
Example:
  cvad_session_recording_agent_servername: myservername.mycompany.com

The cvad_session_recording_agent_broker_protocol defines which protocol will be used to connect to Session Recording Server.
Possible Values are:
HTTPS           (default) : This will use HTTPS as the communications protocol
HTTP                      : This will use HTTP as the communications protocol

The cvad_session_recording_agent_broker_port defines which communication port will be used.
The default is port 443. If the cvad_session_recording_agent_broker_protocol is set to HTTP then set this variable to 80.
Example:
  cvad_session_recording_agent_broker_port: 80

A custom port number can be used if it is also defined on the Session Recording Server.

The cvad_session_recording_agent_install_arguments variable contains the installation arguments that will be used for the
installation. By default, the cvad_session_recording_agent_install_arguments variable uses the value defined in the
cvad_session_recording_agent_versions hash table using cvad_session_recording_agent_version as the hash key.
The cvad_session_recording_agent_install_arguments variable can be customized in the group_vars, host_vars, or even with in the
the playbook itself.

The cvad_session_recording_agent_product_id variable contains the product id relative to the version of the Citrix Session Recording
Agent that is being installed. By default, the .iso file used is defined in the cvad_session_recording_agent_versions hash table
using cvad_session_recording_agent_version as the hash key. The cvad_session_recording_agent_product_id variable can be customized
in the group_vars, host_vars, or even with in the playbook itself.

The cvad_session_recording_agent_install_state variable controls the installation state for the Citrix Session Recording Agent.  This
role does not currently support the removal of the Citrix Session Recording Agent or any of the pre-requisites and plug-ins that were
installed during the installation process.

Possible Values are:
  present         (default) : This will install the Session Recording Agent

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

ctxcommunity.windows.msmq_http

Example Playbook
----------------

    - hosts: servers
      roles:
         - ctxcommunity.cvad.install_session_recording_agent_iso

    - hosts: servers
      roles:
         -  role: ctxcommunity.cvad.install_session_recording_agent_iso
            vars:
              cvad_session_recording_agent_version: 2012

License
-------

GPL-3.0-only

Author Information
------------------

Joe Shonk
