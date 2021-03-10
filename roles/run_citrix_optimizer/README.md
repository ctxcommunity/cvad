Role Name
=========

Role Name: ctxcommunity.cvad.run_citrix_optimizer

This role runs the Citrix Optimizer using a custom defined template.

Requirements
------------

Role Variables
--------------

The defaults/main.yml contains the following variables and default values:

  smb_location: \\\\file1\\automation
  citrix_optimizer_version: 2.8.0.143
  citrix_optimizer_smb_location: "{{ smb_location }}\\\\Citrix\\\\Citrix Optimizer\\\\{{ citrix_optimizer_version }}"
  citrix_optimizer_file: .\\CtxOptimizerEngine.ps1
  citrix_optimizer_template: Citrix_Windows_Server_2019_1809_custom.xml
  citrix_optimizer_template_smb_location: "{{ citrix_optimizer_smb_location }}\\\\Templates"
  citrix_optimizer_arguments: -Source "{{ citrix_optimizer_template_smb_location }}\\\\{{ citrix_optimizer_template }}" -Mode Execute

=======

An SMB location must defined with the Ansible Become User having a minimum of Read-Only access to the Share and folder structure.
The smb_location variable can be defined in the group_vars, host_vars, or even with in the playbook itself.

Sample SMB File and Folder Structure:
  \\\\file1\\automation\\
      Citrix\\
          Citrix Optimizer\\
              2.7.1.1\\
              2.8.0.143\\

The citrix_optimizer_version variable is used to control which version of the Citrix Optimizer will be installed.
This variable is used as the lookup key for the citrix_optimizer_versions hash table.

Possible Values are:
  2.7.1.1         (default) : This run the 2.7.1.1 version of the Citrix Optimizer
  2.8.0.143                 : This run the 2.8.0.143 version of the Citrix Optimizer

The citrix_optimizer_smb_location variable aggregates the SMB location with the folder location including the variable
citrix_optimizer_version as part of the folder location. The citrix_optimizer_smb_location variable can be customized
in the group_vars, host_vars, or even with in the playbook itself.

The citrix_optimizer_file variable contains the Optimizer PowerShell Script that used to apply the template settings.
The citrix_optimizer_file variable can be customized in the group_vars, host_vars, or even with in the
playbook itself.

The citrix_optimizer_template_smb_location and citrix_optimizer_template variables are used to define the
folder path and file name of the Template that will be used. The citrix_optimizer_template_smb_location and
citrix_optimizer_template variables can be customized in the group_vars, host_vars, or even with in the playbook itself.

The citrix_optimizer_arguments variable contains the PowerShell arguments that will be used when the Citrix Optimizer
script is executed. The citrix_optimizer_arguments variable can be customized in the group_vars, host_vars, or even with in the
the playbook itself.

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
         - ctxcommunity.cvad.run_citrix_optimizer

    - hosts: servers
      roles:
      - role: ctxcommunity.cvad.run_citrix_optimizer
        vars:
          citrix_optimizer_template: Citrix_Windows_Server_2019_1809_custom.xml


License
-------

GPL-3.0-only

Author Information
------------------

Joe Shonk
