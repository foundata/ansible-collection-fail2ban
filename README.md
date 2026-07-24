# Ansible collection: `foundata.fail2ban`

This repository contains the `foundata.fail2ban` Ansible Collection. It provides resources to manage [fail2ban](https://github.com/fail2ban/fail2ban), a daemon to ban hosts that cause multiple authentication errors.


<div align="center" id="project-readme-header">
<br>
<br>

**⭐ Found this useful? Support open-source and star this project:**

[![GitHub repository](https://img.shields.io/github/stars/foundata/ansible-collection-fail2ban.svg)](https://github.com/foundata/ansible-collection-fail2ban)

<br>
</div>



## Table of contents<a id="toc"></a>
- [Included content](#content)
- [Dependencies](#dependencies)
- [Licensing, copyright](#licensing-copyright)
- [Author information](#author-information)



## Included content<a id="content"></a>

### Role: `foundata.fail2ban.run`

The primary role in this collection to configure and maintain Fail2Ban, including jail configuration, filters, actions, and related settings. [Its `README.md`](./roles/run/README.md) covers configuration, usage examples, and more:

<!-- ANSIBLE DOCSMITH TOC-FULL run START -->
- [Ansible role: `foundata.fail2ban.run`](roles/run/README.md#ansible-role-foundatafail2banrun)
  - [Table of contents](roles/run/README.md#toc)
  - [Features](roles/run/README.md#features)
  - [Example playbooks, using this role](roles/run/README.md#examples)
  - [Supported tags](roles/run/README.md#tags)
  - [Role variables](roles/run/README.md#variables)
    - [`run_fail2ban_state`](roles/run/README.md#variable-run_fail2ban_state)
    - [`run_fail2ban_autoupgrade`](roles/run/README.md#variable-run_fail2ban_autoupgrade)
    - [`run_fail2ban_service_state`](roles/run/README.md#variable-run_fail2ban_service_state)
    - [`run_fail2ban_service_settings`](roles/run/README.md#variable-run_fail2ban_service_settings)
    - [`run_fail2ban_config_service_dropin_file_name`](roles/run/README.md#variable-run_fail2ban_config_service_dropin_file_name)
    - [`run_fail2ban_jail_settings`](roles/run/README.md#variable-run_fail2ban_jail_settings)
    - [`run_fail2ban_config_jail_dropin_file_name`](roles/run/README.md#variable-run_fail2ban_config_jail_dropin_file_name)
    - [`run_fail2ban_custom_filters`](roles/run/README.md#variable-run_fail2ban_custom_filters)
    - [`run_fail2ban_custom_actions`](roles/run/README.md#variable-run_fail2ban_custom_actions)
  - [Dependencies](roles/run/README.md#dependencies)
  - [Compatibility](roles/run/README.md#compatibility)
  - [External requirements](roles/run/README.md#requirements)
<!-- ANSIBLE DOCSMITH TOC-FULL run END -->



## Dependencies<a id="dependencies"></a>

See `dependencies` in [`galaxy.yml`](./galaxy.yml).



## Licensing, copyright<a id="licensing-copyright"></a>

<!--REUSE-IgnoreStart-->
Copyright (c) 2026 foundata GmbH

This project is licensed under the GNU General Public License v3.0 or later (SPDX-License-Identifier: `GPL-3.0-or-later`), see [`LICENSES/GPL-3.0-or-later.txt`](LICENSES/GPL-3.0-or-later.txt) for the full text.

The [`REUSE.toml`](REUSE.toml) file provides detailed licensing and copyright information in a human- and machine-readable format. This includes parts that may be subject to different licensing or usage terms, such as third-party components. The repository conforms to the [REUSE specification](https://reuse.software/spec/). You can use [`reuse spdx`](https://reuse.readthedocs.io/en/latest/readme.html#cli) to create a [SPDX software bill of materials (SBOM)](https://en.wikipedia.org/wiki/Software_Package_Data_Exchange).
<!--REUSE-IgnoreEnd-->

[![REUSE status](https://api.reuse.software/badge/github.com/foundata/ansible-collection-fail2ban)](https://api.reuse.software/info/github.com/foundata/ansible-collection-fail2ban)



## Author information<a id="author-information"></a>

This project was created and is maintained by foundata GmbH.

Initially based on an [Ansible skeleton](https://foundata.com/en/projects/ansible-skeletons/) developed by [foundata](https://foundata.com/).
