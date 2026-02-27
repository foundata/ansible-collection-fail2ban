# Ansible role: `foundata.fail2ban.run`

The `foundata.fail2ban.run` Ansible role (part of the `foundata.fail2ban` Ansible collection). It provides automated management of [fail2ban](https://github.com/fail2ban/fail2ban) configuration and service state.



## Table of contents<a id="toc"></a>

- [Features](#features)
- [Example playbooks, using this role](#examples)
- [Supported tags](#tags)<!-- ANSIBLE DOCSMITH TOC START -->
- [Role variables](#variables)
  - [`run_fail2ban_state`](#variable-run_fail2ban_state)
  - [`run_fail2ban_autoupgrade`](#variable-run_fail2ban_autoupgrade)
  - [`run_fail2ban_service_state`](#variable-run_fail2ban_service_state)
  - [`run_fail2ban_service_settings`](#variable-run_fail2ban_service_settings)
  - [`run_fail2ban_config_service_dropin_file_name`](#variable-run_fail2ban_config_service_dropin_file_name)
  - [`run_fail2ban_jail_settings`](#variable-run_fail2ban_jail_settings)
  - [`run_fail2ban_config_jail_dropin_file_name`](#variable-run_fail2ban_config_jail_dropin_file_name)
  - [`run_fail2ban_custom_filters`](#variable-run_fail2ban_custom_filters)
  - [`run_fail2ban_custom_actions`](#variable-run_fail2ban_custom_actions)
<!-- ANSIBLE DOCSMITH TOC END -->
- [Dependencies](#dependencies)
- [Compatibility](#compatibility)
- [External requirements](#requirements)



## Features<a id="features"></a>

* Custom filters and actions:
  * Define custom fail2ban filters and actions as simple YAML dictionaries or raw content.
* Sane defaults (see `__run_fail2ban_jail_settings_defaults` in [`./vars/main.yml`](./vars/main.yml) for a complete list)
  * Platform-aware ban actions (`nftables`, `firewalld`, `ufw)` and log backends (`systemd` journal).
  * Built-in `sshd` jail enabled out of the box.
* Preserve distribution defaults by managing only drop-in configuration files in `/etc/fail2ban/fail2ban.d/` and `/etc/fail2ban/jail.d/`, ensuring compatibility across a wide range of distributions.
* Simple configuration with a layered merge system:
  * Internal defaults, platform-specific overrides, and user settings are merged automatically.
  * User-provided values always take highest priority.



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

Installation with custom jail settings (overriding defaults) and a custom filter:

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
        run_fail2ban_jail_settings:
          DEFAULT:
            bantime: "1h"
            findtime: "30m"
            maxretry: 5
            destemail: "admin@example.com"
          sshd:
            maxretry: 3
        run_fail2ban_custom_filters:
          my-app:
            Definition:
              failregex: '^Authentication failure for .* from <HOST>$'
```

Installation with a custom action using raw content:

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
        run_fail2ban_jail_settings:
          DEFAULT:
            bantime: "15m"
          my-app:
            enabled: true
            filter: "my-app"
            logpath: "/var/log/my-app/access.log"
            backend: "auto"
            maxretry: 5
            action: "notify-slack"
        run_fail2ban_custom_actions:
          notify-slack:
            content: |
              [Definition]
              actionban = curl -X POST -H 'Content-type: application/json' --data '{"text":"Banned <ip>"}' https://hooks.slack.example.com/services/xxx
              actionunban = curl -X POST -H 'Content-type: application/json' --data '{"text":"Unbanned <ip>"}' https://hooks.slack.example.com/services/xxx
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

The following variables can be configured for this role:

| Variable | Type | Required | Default | Description (abstract) |
|----------|------|----------|---------|------------------------|
| `run_fail2ban_state` | `str` | No | `"present"` | Determines whether the managed resources should be `present` or `absent`.<br><br>`present` ensures that required components, such as software packages, are installed and configured.<br><br>`absent` reverts changes as much as possible, such as […](#variable-run_fail2ban_state) |
| `run_fail2ban_autoupgrade` | `bool` | No | `false` | If set to `true`, all managed packages will be upgraded during each Ansible run (e.g., when the package provider detects a newer version than the currently installed one). |
| `run_fail2ban_service_state` | `str` | No | `"enabled"` | Defines the status of the service(s).<br><br>`enabled`: Service is running and will start automatically at boot.<br><br>`disabled`: Service is stopped and will not start automatically at boot.<br><br>`running` Service is running but will not start […](#variable-run_fail2ban_service_state) |
| `run_fail2ban_service_settings` | `dict` | No | `{}` | Fail2ban service configuration values.<br><br>These settings are placed in a drop-in configuration file in `/fail2ban.d`. They take highest priority, overriding internal defaults (see `__run_fail2ban_service_settings_defaults` in `vars/main.yml`) and […](#variable-run_fail2ban_service_settings) |
| `run_fail2ban_config_service_dropin_file_name` | `str` | No | `"99-managed.local"` | Filename of the drop-in configuration file to be placed in `/fail2ban.d`. Defaults to `99-managed.local`. The `99-` prefix in combination with the `.local` extension ensures later loading and thus higher precedence over files with lower-numbered […](#variable-run_fail2ban_config_service_dropin_file_name) |
| `run_fail2ban_jail_settings` | `dict` | No | `{}` | Fail2ban jail configuration values.<br><br>These settings are placed in a drop-in configuration file in `/jail.d`. They take highest priority, overriding internal defaults (see `__run_fail2ban_jail_settings_defaults` in `vars/main.yml`) and […](#variable-run_fail2ban_jail_settings) |
| `run_fail2ban_config_jail_dropin_file_name` | `str` | No | `"99-managed.local"` | Filename of the drop-in configuration file to be placed in `/jail.d`. Defaults to `99-managed.local`. The `99-` prefix in combination with the `.local` extension ensures later loading and thus higher precedence over files with lower-numbered prefixes […](#variable-run_fail2ban_config_jail_dropin_file_name) |
| `run_fail2ban_custom_filters` | `dict` | No | `{}` | Custom filter definitions to be placed in `/filter.d/`. Filters define the regex patterns used to detect intrusion attempts in log files.<br><br>Use filter names as top-level YAML keys (will create `filter.d/.conf`) Use section names as 2nd-level […](#variable-run_fail2ban_custom_filters) |
| `run_fail2ban_custom_actions` | `dict` | No | `{}` | Custom action definitions to be placed in `/action.d/`. Actions define what happens when an IP is banned or unbanned (e.g., firewall rules, notifications).<br><br>Use action names as top-level YAML keys (will create `action.d/.conf`) Use section […](#variable-run_fail2ban_custom_actions) |

### `run_fail2ban_state`<a id="variable-run_fail2ban_state"></a>

[*⇑ Back to ToC ⇑*](#toc)

Determines whether the managed resources should be `present` or `absent`.

`present` ensures that required components, such as software packages, are
installed and configured.

`absent` reverts changes as much as possible, such as removing packages,
deleting created users, stopping services, restoring modified settings, …

- **Type**: `str`
- **Required**: No
- **Default**: `"present"`
- **Choices**: `present`, `absent`



### `run_fail2ban_autoupgrade`<a id="variable-run_fail2ban_autoupgrade"></a>

[*⇑ Back to ToC ⇑*](#toc)

If set to `true`, all managed packages will be upgraded during each Ansible
run (e.g., when the package provider detects a newer version than the
currently installed one).

- **Type**: `bool`
- **Required**: No
- **Default**: `false`



### `run_fail2ban_service_state`<a id="variable-run_fail2ban_service_state"></a>

[*⇑ Back to ToC ⇑*](#toc)

Defines the status of the service(s).

`enabled`: Service is running and will start automatically at boot.

`disabled`: Service is stopped and will not start automatically at boot.

`running` Service is running but will not start automatically at boot.
This can be used to start a service on the first Ansible run without
enabling it for boot.

`unmanaged`: Service will not start at boot, and Ansible will not manage
its running state. This is primarily useful when services are monitored
and managed by systems other than Ansible.

The singular form (`service`) is used for simplicity. However, the defined
status applies to all services if multiple are being managed by this role.

- **Type**: `str`
- **Required**: No
- **Default**: `"enabled"`
- **Choices**: `enabled`, `disabled`, `running`, `unmanaged`



### `run_fail2ban_service_settings`<a id="variable-run_fail2ban_service_settings"></a>

[*⇑ Back to ToC ⇑*](#toc)

Fail2ban service configuration values.

These settings are placed in a drop-in configuration file in
`<config dir>/fail2ban.d`. They take highest priority, overriding internal
defaults (see `__run_fail2ban_service_settings_defaults` in `vars/main.yml`)
and platform-specific overrides.


Use standard Fail2ban section names as top-level YAML keys, and place their
corresponding option names and values inside each section. This YAML structure
is directly translated into Fail2ban's INI format, preserving section names
and option names exactly as written:

- Each INI section (`[SECTION]`) becomes a YAML mapping key.
- Each INI option inside that section becomes a YAML key-value pair.

Special cases:

- For boolean values, use `true`/`false` (these will be converted to strings
  by the role as needed).
- For multiline values, use normal YAML block scalars (`|`). The role will
  automatically handle indentation:
  ```
  logpath: |
    /var/log/auth.log
    /var/log/secure
  ```
- Settings in Fail2ban's `DEFAULT` section are getting applied to all parts
  of the configuration unless overridden in individual section definitions.

Example:

```yaml
DEFAULT:
  loglevel: "INFO"
  logtarget: "/var/log/fail2ban.log"
Thread:
  stacksize: 0
```

will result in

```ini
[DEFAULT]
loglevel = INFO
logtarget = /var/log/fail2ban.log

[Thread]
stacksize = 0
```

The official documentation provides general configuration guidance:

- [`man fail2ban`](https://manpages.debian.org/testing/fail2ban/fail2ban.1.en.html)
- https://github.com/fail2ban/fail2ban/wiki/Best-practice
- https://github.com/fail2ban/fail2ban/wiki/Proper-fail2ban-configuration

The unmodified default configuration is already a good starting point and
usually doesn't require changes when using common services like SSH or NGINX.
If empty dict (default), only the internal defaults and any platform-specific
overrides apply.

- **Type**: `dict`
- **Required**: No
- **Default**: `{}`



### `run_fail2ban_config_service_dropin_file_name`<a id="variable-run_fail2ban_config_service_dropin_file_name"></a>

[*⇑ Back to ToC ⇑*](#toc)

Filename of the drop-in configuration file to be placed in
`<config dir>/fail2ban.d`. Defaults to `99-managed.local`. The `99-`
prefix in combination with the `.local` extension ensures later loading and
thus higher precedence over files with lower-numbered prefixes or `.conf`
extension (cf. `man jail.conf`).

If a non-default filename is used, any existing
`<config dir>/fail2ban.d/99-managed.local` from previous Ansible runs
will be removed automatically to prevent conflicts.

- **Type**: `str`
- **Required**: No
- **Default**: `"99-managed.local"`



### `run_fail2ban_jail_settings`<a id="variable-run_fail2ban_jail_settings"></a>

[*⇑ Back to ToC ⇑*](#toc)

Fail2ban jail configuration values.

These settings are placed in a drop-in configuration file in
`<config dir>/jail.d`. They take highest priority, overriding internal
defaults (see `__run_fail2ban_jail_settings_defaults` in `vars/main.yml`) and
platform-specific overrides.

Use standard Fail2ban section names as top-level YAML keys, and place their
corresponding option names and values inside each section. This YAML structure
is directly translated into Fail2ban's INI format, preserving section names
and option names exactly as written:

- Each INI section (`[SECTION]`) becomes a YAML mapping key.
- Each INI option inside that section becomes a YAML key-value pair.

Special cases:

- For boolean values, use `true`/`false` (these will be converted to strings
  by the role as needed).
- For multiline values, use normal YAML block scalars (`|`). The role will
  automatically handle indentation:
  ```
  logpath: |
    /var/log/auth.log
    /var/log/secure
  ```
- Settings in Fail2ban's `DEFAULT` section are getting applied to all jails
  unless overridden in individual jail definitions.

Example:

```yaml
DEFAULT:
  enabled: false
  maxretry: 3
  ignoreself: true
  ignoreip: "127.0.0.1/8 ::1 192.168.0.0/16 10.0.0.0/8 my-trusted-host.example.com"
sshd:
  enabled: true
nginx-http-auth:
  enabled: true
  port: "http,https"
  logpath: "/var/log/nginx/error.log"
```

will result in

```ini
[DEFAULT]
enabled = false
maxretry = 3
ignoreself = true
ignoreip = 127.0.0.1/8 ::1 192.168.0.0/16 10.0.0.0/8 my-trusted-host.example.com

[sshd]
enabled = true

[nginx-http-auth]
enabled = true
port = http,https
logpath = /var/log/nginx/error.log
```

The official documentation provides general configuration guidance:

- [`man jail.conf`](https://manpages.debian.org/testing/fail2ban/jail.conf.5.en.html)
- [`man fail2ban-regex`](https://manpages.debian.org/testing/fail2ban/fail2ban-regex.1.en.html)
- https://github.com/fail2ban/fail2ban/wiki/Best-practice

The unmodified default configuration is already a good starting point and
usually doesn't require changes when using common services like SSH or NGINX
(beside enabling the jail). If empty dict (default), only the internal
defaults, and platform overrides apply.

- **Type**: `dict`
- **Required**: No
- **Default**: `{}`



### `run_fail2ban_config_jail_dropin_file_name`<a id="variable-run_fail2ban_config_jail_dropin_file_name"></a>

[*⇑ Back to ToC ⇑*](#toc)

Filename of the drop-in configuration file to be placed in
`<config dir>/jail.d`. Defaults to `99-managed.local`. The `99-`
prefix in combination with the `.local` extension ensures later loading and
thus higher precedence over files with lower-numbered prefixes or `.conf`
extension (cf. `man jail.conf`).

If a non-default filename is used, any existing
`<config dir>/jail.d/99-managed.local` from previous Ansible runs
will be removed automatically to prevent conflicts.

- **Type**: `str`
- **Required**: No
- **Default**: `"99-managed.local"`



### `run_fail2ban_custom_filters`<a id="variable-run_fail2ban_custom_filters"></a>

[*⇑ Back to ToC ⇑*](#toc)

Custom filter definitions to be placed in `<config dir>/filter.d/`. Filters
define the regex patterns used to detect intrusion attempts in log files.

Use filter names as top-level YAML keys (will create `filter.d/<name>.conf`)
Use section names as 2nd-level YAML keys, and place their corresponding option
names and values inside each section. This YAML structure is directly
translated into Fail2ban's INI format, preserving section names and option
names exactly as written:

- Each INI section (`[SECTION]`) becomes a YAML mapping key.
- Each INI option inside that section becomes a YAML key-value pair.

Special cases:

- For boolean values, use `true`/`false` (these will be converted to strings
  by the role as needed).
- There is a `content` key, allowing you to put raw lines for the filter as
  the value without any special treatment. This is useful if you already have
  existing filters and do not want to convert their definitions into YAML:
  ```yaml
  my-complex-filter:
    content: |
      [Definition]
      failregex = ^<HOST> - .*$
      [...]
  ```
Example:

```yaml
dropbear-complex:
  INCLUDES:
    before: "common.conf"
  Definition:
    _daemon: "dropbear"
    failregex: |-
      ^[Ll]ogin attempt for nonexistent user ('.*' )?from <HOST>:\d+$
      ^[Bb]ad (PAM )?password attempt for .+ from <HOST>(:\d+)?$
```

will create `filter.d/dropbear-complex.local` with the content:

```ini
[INCLUDES]
before = common.conf

[Definition]
_daemon = dropbear
failregex = ^[Ll]ogin attempt for nonexistent user ('.*' )?from <HOST>:\d+$
            ^[Bb]ad (PAM )?password attempt for .+ from <HOST>(:\d+)?$
```

The official documentation provides general configuration guidance:

- [`man jail.conf`](https://manpages.debian.org/testing/fail2ban/jail.conf.5.en.html)
- [`man fail2ban-regex`](https://manpages.debian.org/testing/fail2ban/fail2ban-regex.1.en.html)
- https://github.com/fail2ban/fail2ban/wiki/Best-practice

Most users do not need custom filter as fail2ban ships with filters for common
services like SSH or NGINX. If empty dict (default), no custom filters are
managed.

- **Type**: `dict`
- **Required**: No
- **Default**: `{}`



### `run_fail2ban_custom_actions`<a id="variable-run_fail2ban_custom_actions"></a>

[*⇑ Back to ToC ⇑*](#toc)

Custom action definitions to be placed in `<config dir>/action.d/`. Actions
define what happens when an IP is banned or unbanned (e.g., firewall rules,
notifications).

Use action names as top-level YAML keys (will create `action.d/<name>.conf`)
Use section names as 2nd-level YAML keys, and place their corresponding option
names and values inside each section. This YAML structure is directly
translated into Fail2ban's INI format, preserving section names and option
names exactly as written:

- Each INI section (`[SECTION]`) becomes a YAML mapping key.
- Each INI option inside that section becomes a YAML key-value pair.

Special cases:

- For boolean values, use `true`/`false` (these will be converted to strings
  by the role as needed).
- There is a `content` key, allowing you to put raw lines for the action as
  the value without any special treatment. This is useful if you already have
  existing action and do not want to convert their definitions into YAML:
  ```yaml
  my-complex-notify-slack:
    content: |
      [Definition]
      actionban = curl -X POST -H 'Content-type: application/json' --data '{"text":"Banned <ip>"}' ...
      actionunban = curl -X POST -H 'Content-type: application/json' --data '{"text":"Unbanned <ip>"}'
  ```

Example:

```yaml
my-complex-notify-slack:
  INCLUDES:
    before: "foobar.conf"
  Definition:
    actionban: >-
      curl -X POST -H 'Content-type: application/json' --data '{"text":"Banned <ip>"}' ...
    actionunban: >-
      curl -X POST -H 'Content-type: application/json' --data '{"text":"Unbanned <ip>"}' ...
```

will create `action.d/my-complex-notify-slack.local` with the content:

```ini
[INCLUDES]
before = foobar.conf

[Definition]
actionban = curl -X POST -H 'Content-type: application/json' --data '{"text":"Banned <ip>"}' ...
actionunban = curl -X POST -H 'Content-type: application/json' --data '{"text":"Unbanned <ip>"}' ...
```

The official documentation provides general configuration guidance:

- [`man jail.conf`](https://manpages.debian.org/testing/fail2ban/jail.conf.5.en.html)
- https://github.com/fail2ban/fail2ban/wiki/Best-practice

Most users do not need custom actions as fail2ban ships with actions for
common firewall backends. If empty dict (default), no custom actions are
managed.

- **Type**: `dict`
- **Required**: No
- **Default**: `{}`




<!-- ANSIBLE DOCSMITH MAIN END -->

## Dependencies<a id="dependencies"></a>

See `dependencies` in [`meta/main.yml`](./meta/main.yml).



## Compatibility<a id="compatibility"></a>

See `min_ansible_version` in [`meta/main.yml`](./meta/main.yml) and `__run_fail2ban_supported_platforms` in [`vars/main.yml`](./vars/main.yml).



## External requirements<a id="requirements"></a>

There are no special requirements not covered by Ansible itself.
