Role Name
=========

Onboards a new pc.  Will do the following tasks:

create users/groups
install packages
start/enable services
mount specified remote filesystems (nfs/smb)

Requirements
------------

None

Role Variables
--------------

Recommended way to include these variables is using host_vars and create vars.yml file containing the values for the variables listed.  Use a vault.yml for user credentials.  These should be created under each host.

Ex.

Directory structure.

host_vars\[hostname]\vars.yml
                     vault.yml


# user to mount a smb share
base_system_smb_mountuser: rich

# not referenced in the role, after testing should
# user to mount a smb share
smb_mountuser: rich

# not referenced in the role, after testing should
# be removed
# ipv4 address of host
smb_host_ipv4_address:

# if the below is false only the users in
# base_system_mandatory_user_accounts will be onboarded
base_system_onboard_all_users: true

# mountpoints to create on host
base_system_mountpoints:
  - path:
    owner:
    mode:

# this will be used with ansible.posix.mount
base_system_mounts:
  - src:
    mount_point:
    fstype:
    boot: true
    dump: '0'
    opts:
    state: mounted

# groups to create
base_system_user_groups:
  - group_name:
    group_gid:

# user accounts
# if sudo_usre attribute is true the user will
# have sudo rights on the host
base_system_user_accounts:
  - user_name:
    password:
    groups:
    uid:
    sudo_user: false
    shell:

# if base_system_onboard_all_users = false only the users in
# base_system_mandatory_user_accounts will be onboarded
base_system_mandatory_user_accounts:
  - user_name:
    password:
    groups:
    uid:
    sudo_user: false
    shell:

# variable for rhel-system-roles.sshd

sshd:
  PermitRootLogin: false
  PasswordAuthentication: false
  PubkeyAuthenticiation: true

# list of services to start/enable

base_system_svcs:
  - sshd

# list of packages to install
base_system_pkg_manifest:
  - firewalld

# FreeBSD specific variables, these don't need to be included for other OSs
# Be sure to define all the variables below, the script will decide what to use
# depending on how the host is setup.

# boot device name (BIOS)
base_system_bios_device:
# index of the boot device index (BIOS)
base_system_bios_device_index:
# boot device name (UEFI)
base_system_uefi_device:

Dependencies
------------

None.

Example Playbook
----------------

- name: base_system role
  hosts: node6.rocky93.example.com
  become: true

  vars:

    base_system_pkg_manifest:
      - bash-completion
      - vim
      - nfs-utils

    # make sure to put all password related variables in an ansible vault
    base_system_smb_mountuser: bob
    base_system_smb_mountuser_pw: secretpassword

    base_system_mountpoints:
      - path: /mnt/nfs/priv
        owner: bob
        group: nfsusers
        mode: "0277"

    base_system_mounts:
      - src: 172.16.20.85:/exp/nfs/priv
        mount_point: /mnt/nfs/priv
        fstype: nfs
        boot: true
      	dump: '0'
        opts: "_netdev,defaults,sec=sys"
        state: mounted

    base_system_user_groups:
      - group_name: nfsusers
        group_gid: 1010
      - group_name: bob
        group_gid: 1050

    base_system_user_accounts:
      - user_name: bob
        password: pa$$w0rD!
        groups: nfsusers,users
        uid: 1050
        sudo_user: true
        shell: /bin/bash

    # if using FreeBSD host
    # boot device name (BIOS)
    base_system_bios_device: nda0
    # index of the boot device index (BIOS)
    base_system_bios_device_index: 1
    # boot device name (UEFI)
    base_system_uefi_device: nda0p1

  roles:

    - name: base-system

License
-------

BSD

Author Information
------------------

streamline-it
