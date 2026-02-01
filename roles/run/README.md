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










## Features<a id="features"></a>

Main features:

* Sane, secure defaults:
  * No jails enabled by default (explicit opt-in philosophy).
  * Auto-detection of firewall backend (`nftables`, `firewalld`, or `iptables`) and log backend (`systemd` or `auto`).
  * Sensible ban times and retry limits.
  * See `__run_fail2ban_jail_defaults_defaults` in `./vars/main.yml` for a complete list.
* Simple jail management: Enable and configure any jail with a single dictionary entry.
* Full flexibility: Any fail2ban option can be passed through without the role needing explicit support.
* Drop-in configuration: Preserves distribution defaults while applying managed settings.
* Optional custom filter and action support: Define your own intrusion patterns and ban actions when needed.
* Cross-platform: Works across major Linux distributions with automatic adaptation to platform-specific defaults.




## What this role controls<a id="what-this-role-controls"></a>

fail2ban uses a layered configuration system with separate files for different concerns. This role manages the following components:
```
/etc/fail2ban/
│
├── fail2ban.conf                    # Daemon settings (logging, database, socket)
│   └── Controlled by: run_fail2ban_settings
│
├── jail.d/
│   └── 00-managed.conf              # Jail defaults + individual jail definitions
│       └── Controlled by: run_fail2ban_jail_defaults
│                          run_fail2ban_jails
│                          run_fail2ban_ignoreip
│                          run_fail2ban_config_dropin_file_name
│
├── filter.d/
│   ├── sshd.conf                    # Built-in filter (not managed)
│   ├── nginx-http-auth.conf         # Built-in filter (not managed)
│   └── <custom>.conf                # Custom filters (optional)
│       └── Controlled by: run_fail2ban_custom_filters
│
└── action.d/
    ├── nftables-multiport.conf      # Built-in action (not managed)
    ├── iptables-multiport.conf      # Built-in action (not managed)
    └── <custom>.conf                # Custom actions (optional)
        └── Controlled by: run_fail2ban_custom_actions
```

### Configuration layers explained

**Daemon settings (`fail2ban.conf`)**
Controls the fail2ban service itself: logging level and destination, database location and purge age, PID file and socket paths. Most users can leave these at defaults.

**Jail definitions (`jail.d/`)**
Jails are the core concept of fail2ban. Each jail:
- Monitors one or more log files for intrusion patterns
- Uses a *filter* to detect failures via regex
- Triggers an *action* (typically a firewall ban) after threshold is reached

This role uses a drop-in file (`00-managed.conf`) to define jail defaults and individual jails, leaving distribution-provided configurations untouched.

**Filters (`filter.d/`)**
Filters contain the regex patterns that identify failed authentication attempts or other malicious activity in log files. fail2ban ships with filters for most common services (SSH, nginx, Apache, Postfix, etc.). Custom filters are only needed for applications with unique log formats.

**Actions (`action.d/`)**
Actions define what happens when an IP is banned or unbanned. The default action adds firewall rules to block the offending IP. fail2ban ships with actions for `nftables`, `iptables`, `firewalld`, and various notification methods. Custom actions are typically only needed for specialized notifications (Slack, PagerDuty, etc.).

### How jails reference filters and actions

A jail *references* filters and actions by name:
```ini
[my-custom-app]
enabled   = true
filter    = my-custom-app           # → Uses filter.d/my-custom-app.conf
action    = nftables-multiport      # → Uses action.d/nftables-multiport.conf
            notify-slack            # → Uses action.d/notify-slack.conf
logpath   = /var/log/my-app.log
maxretry  = 5
```

Filters and actions are reusable components—one filter can be used by multiple jails, a