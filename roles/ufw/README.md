ufw
===

Installing ufw and applying rules

Role Variables
--------------

- `ufw_ports`: default port rules
- `host_ufw`: host ufw rules
  
  ```yml
  # e.g.
  # place this var in `inventories/demo/base.yml`
  host_ufw:
    extra_ports:
      - name: test_ufw
        port: "9527"
        rule: deny
  ```

Example Playbook
----------------

```yml
---
# version: 2.10
- name: initialize server
  become: true
  become_user: root
  become_method: sudo
  gather_facts: false
  hosts: all
  vars_files:
    - defaults/main.yml

  pre_tasks:
    - name: detect preconditions
      import_role:
        name: detect
      tags: [detect]

  roles:
    - role: ufw
      tags: [security, network]
```

License
-------

BSD

Author Information
------------------

Octobug, whalevocal@gmail.com
