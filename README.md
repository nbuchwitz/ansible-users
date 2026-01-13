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
    # don't remove existing sshkeys (default: True)
    sshkey_exclusive: False
    users:
      - name: janedoe
      - name: ceo
  roles:
    - nbuchwitz.users
```

This can also be configured per user:

```
---
- hosts: example-host
  become: true
  vars:
    users:
      - name: janedoe
        # allow other sshkeys for this user only
        sshkey_exclusive: False
      - name: ceo
  roles:
    - nbuchwitz.users
```

For the root account, use `root_sshkey_exclusive` (falls back to `sshkey_exclusive` if not set):

```
---
- hosts: example-host
  become: true
  vars:
    # keep manually added keys for root, but enforce exclusive keys for regular users
    root_sshkey_exclusive: False
    users:
      - name: janedoe
        root_ssh_keys: True
      - name: ceo
        root_ssh_keys: True
  roles:
    - nbuchwitz.users
```

### Exclude users on specific hosts

If you define users globally (e.g., in `group_vars/all.yml`) but need to exclude specific users on certain hosts, use `users_exclude`:

```
# group_vars/all.yml
users:
  - name: johndoe
  - name: janedoe
  - name: ceo
```

```
# host_vars/db.yml
users_exclude:
  - johndoe
```

This excludes `johndoe` from all user management tasks on the host `db`, without affecting other hosts.