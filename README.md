# User management

This role creates user accounts and distributes the sshkeys from a sshkey repository

## Example playbooks

```
---
- hosts: example-host
  become: true
  vars:
    # global groups
    extra_groups:
      - dialout # allow access of serial interface
    users:
      - name: nbw
        # distribute sshkeys for root account too
        root_ssh_keys: True
        # add additional user groups
        extra_groups:
          - cdrom

      - name: janedoe

      - name: ceo
        # prohibit privilege  escalation with sudo
        sudo: False

      - name: pi
        # ensure that the pi user is absent
        state: absent
  roles:
    - nbuchwitz.users
```

### Central path for sshkey repo

To prevent multiple instances of the sshkey repository the path is configurable. Keep in mind, that local paths in playbooks, which are shared with others, are bad practice!

```
---
- hosts: example-host
  become: true
  vars:
    # set path for sshkey repository (default: ./ssh-keys)
    sshkey_repo: ~/admin/sshkeys
    users:
      - name: janedoe
      - name: ceo
  roles:
    - nbuchwitz.users
```

### Allow other sshkeys

Sometimes it can be necessary to allow other sshkeys, which are not distributed via Ansible (for example Proxmox VE cluster):

```
---
- hosts: example-host
  become: true
  vars:
    # don't remove existing sshkeys 
    sshkey_exclusive: False
    users:
      - name: janedoe
      - name: ceo
  roles:
    - nbuchwitz.users
```
