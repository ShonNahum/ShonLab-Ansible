# common_ubuntu

Installs and configures baseline packages on Ubuntu systems.

## Requirements

- Target must be an Ubuntu system (Focal, Jammy, or Noble)
- Collections: `community.general`

## Role Variables

All variables are defined in `defaults/main.yml` and can be overridden via `group_vars` or `host_vars`.

| Variable | Default | Description |
|---|---|---|
| `common_ubuntu_packages` | `[curl, wget, git, vim, ...]` | List of apt packages to install |
| `common_ubuntu_upgrade` | `false` | Whether to run `apt upgrade` |
| `common_ubuntu_upgrade_type` | `safe` | Upgrade type: `dist`, `full`, `safe`, `yes` |
| `common_ubuntu_auto_updates` | `true` | Enable `unattended-upgrades` |
| `common_ubuntu_timezone` | `UTC` | System timezone |
| `common_ubuntu_set_hostname` | `false` | Set hostname from inventory name |

## Tags

| Tag | Description |
|---|---|
| `common` | All tasks in this role |
| `packages` | Package installation and upgrade |
| `upgrade` | Package upgrade only |
| `timezone` | Timezone configuration |
| `hostname` | Hostname configuration |
| `security` | Security-related tasks |

## Example Usage

```yaml
# In a playbook
- hosts: ubuntu
  become: true
  roles:
    - role: common_ubuntu

# Override variables in group_vars/ubuntu.yml
common_ubuntu_packages:
  - curl
  - wget
  - git
common_ubuntu_timezone: "Asia/Jerusalem"
```

## Dependencies

None.
