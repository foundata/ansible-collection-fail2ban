# Ansible role: `foundata.fail2ban.run`

The `foundata.fail2ban.run` Ansible role (part of the `foundata.fail2ban` Ansible collection).



## Table of contents<a id="toc"></a>

- [Example playbooks, using this role](#examples)
- [Supported tags](#tags)<!-- ANSIBLE DOCSMITH TOC START -->
- [Role variables](#variables)
<!-- ANSIBLE DOCSMITH TOC END -->
- [Dependencies](#dependencies)
- [Compatibility](#compatibility)
- [External requirements](#requirements)



## Example playbooks, using this role<a id="examples"></a>

Installation with automatic upgrade:

```yaml
---

- name: "Initialize the foundata.fail2ban.run role"
  hosts: localhost
  gather_facts: false
  tasks:

    - name: "Trigger invocation of the foundata.fail2ban.run role"
      ansible.builtin.include_role:
        name: "foundata.fail2ban.run"
      vars:
        run_fail2ban_autoupgrade: true
```

Uninstall:

```yaml
---

- name: "Initialize the foundata.fail2ban.run role"
  hosts: localhost
  gather_facts: false
  tasks:

    - name: "Trigger invocation of the foundata.fail2ban.run role"
      ansible.builtin.include_role:
        name: "foundata.fail2ban.run"
      vars:
        run_fail2ban_state: "absent"
```



## Supported tags<a id="tags"></a>

It might be useful and faster to only call parts of the role by using tags:

- `run_fail2ban_setup`: Manage basic resources, such as packages or service users.
- `run_fail2ban_config`: Manage settings, such as adapting or creating configuration files.
- `run_fail2ban_service`: Manage services and daemons, such as running states and service boot configurations.

There are also tags usually not meant to be called directly but listed for the sake of completeness** and edge cases:

- `run_fail2ban_always`, `always`: Tasks needed by the role itself for internal role setup and the Ansible environment.


<!-- ANSIBLE DOCSMITH MAIN START -->

## Role variables<a id="variables"></a>

See [`defaults/main.yml`](./defaults/main.yml) for all available role parameters and their description. [`vars/main.yml`](./vars/main.yml) contains internal variables you should not override (but their description might be interesting).

Additionally, there are variables read from other roles and/or the global scope (for example, host or group vars) as follows:

- None right now.

<!-- ANSIBLE DOCSMITH MAIN END -->

## Dependencies<a id="dependencies"></a>

See `dependencies` in [`meta/main.yml`](./meta/main.yml).



## Compatibility<a id="compatibility"></a>

See `min_ansible_version` in [`meta/main.yml`](./meta/main.yml) and `__run_fail2ban_supported_platforms` in [`vars/main.yml`](./vars/main.yml).



## External requirements<a id="requirements"></a>

There are no special requirements not covered by Ansible itself.
