# Navidrome
Navidrome Music Streaming Server from public Debian package.

## [Requirements](https://github.com/r-pufky/ansible_navirdrome/blob/main/meta/main.yml)
**galaxy-ng** roles cannot be used independently. Part of
[r_pufky.media](https://github.com/r-pufky/ansible_collection_media)
collection.

* RAM: ~512MB
* Install size: ~1GB

## Role Variables
Detailed variable use documented in defaults. See usage for role operation.

* [defaults](https://github.com/r-pufky/ansible_navidrome/tree/main/defaults/main/main.yml) -
  User configurable options.

* [vars](https://github.com/r-pufky/ansible_navidrome/tree/main/vars/main.yml) -
  Role default options (may be referenced in defaults).

* [ports](https://github.com/r-pufky/ansible_navidrome/blob/main/defaults/main/ports.yml) -
  Ports are **not** managed (defined for external use).

## Usage

### Feature Flags
Tasks are gated by feature flags and executed in the following order.

  Step | Flag                      | Notes
 ------|---------------------------|-------
  1    | navidrome_flg_backup      | Backup existing config. Exits role.
  2    | navidrome_flg_restore     | Restore config, SQLite DB. Exits role.
  3    | navidrome_flg_maintenance | Preform role maintenance tasks.
  4    | navidrome_flg_install     | Install required packages, users, etc.
  5    | navidrome_flg_config      | Install user-defined config.
  7    | navidrome_flg_start       | Start service and validate install.

## Example Playbooks

### New Deployments
A minimal config will be deployed if no configuration is specified. This will
deploy a working Navidrome install using **/var/lib/navidrome** for runtime
data/SQLite and **/opt/navidrome/music** for base music library data.

``` yaml
- name: 'Navidrome'
  ansible.builtin.include_role:
    name: 'r_pufky.media.navidrome'
```

Once configured take a backup of configuration.

``` yaml
- name: 'Backup Navidrome'
  ansible.builtin.include_role:
    name: 'r_pufky.media.navidrome'
  vars:
    navidrome_flg_backup: true
    navidrome_cfg_backup_dir: 'host_vars/navi/data'
```

See [Existing Deployments](#existing-deployments) for reproducible
deployments and configuring a deployment with a different database backend.

### Existing Deployments
Manage additional files and directories (data storage locations, certificates,
etc.) outside of role if used **within** the config. Must be accessible to
**navidrome_srv_user** account.

Non-SQLite databases must be configured and accessible before executing.

host_vars/pngx.example.com/navidrome.toml
``` ini
# Minimal configuration for Navidrome.
#
# https://www.navidrome.org/docs/usage/configuration/options

DataFolder = "/var/lib/navidrome"
MusicFolder = "/d/music"  # Must be created outside of role.
DefaultTheme = "Dark"
LastFM.ApiKey = "{{ vault_navidrome_last_fm_api_key }}"
```

``` yaml
- name: 'New Navidrome Deployment'
  ansible.builtin.include_role:
    name: 'r_pufky.media.navidrome'
  vars:
    navidrome_cfg_file: 'host_vars/navi.example.com/navidrome.toml'
```

Additional music locations and user configuration done through WebUI.

## Development
Configure [environment](https://r-pufky.github.io/ansible_collection_docs/ansible/environment)

Run all unit tests:
``` bash
molecule test --all
```

Testing variables:
 Variable          | type | Description
-------------------|------|-------------
 url_inject_enable | bool | Disable **get_url** to inject files locally.

### Releases
[Semantic versioning](https://semver.org/spec/v2.0.0) focused on service
deployment with templated configuration to minimize role churn due to
inconsistent and rapid rolling release cycle.

 Release | Debian | Ansible | Navidrome | Notes
---------|--------|---------|-----------|-------
 3.x.x   | 13     | 2.20    | v0.60.3   | Ansible 2.20, feature flags, and semantic versioning.
 2.x.x   | 13     | 2.18    | v0.58.0   | Migrate to r_pufky.media.
 1.x.x   | 13     | 2.18    | v0.58.0   | Migration from private repository.

## Issues
Create a bug and provide as much information as possible.

Associate pull requests with a submitted bug.

## License
[AGPL-3.0 License](https://www.tldrlegal.com/license/gnu-affero-general-public-license-v3-agpl-3-0)
 [(direct link)](https://github.com/r-pufky/ansible_navidrome/blob/main/LICENSE)

## Author Information
PGP Fingerprint: [466EEC2B67516C7117C85CE3A0BC35D16698BAB9](https://keys.openpgp.org/vks/v1/by-fingerprint/466EEC2B67516C7117C85CE3A0BC35D16698BAB9)
| [github gist](https://gist.github.com/r-pufky/a8df36977c55b5bb20829267c4c49d22)
