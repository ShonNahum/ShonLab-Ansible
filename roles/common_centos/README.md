# common_centos

Installs and configures baseline packages on CentOS/RHEL systems.

## Requirements

- Target must be a CentOS/RHEL system (EL 8 or 9)
- Collections: `community.general`, `ansible.posix`

## Role Variables

All variables are defined in `defaults/main.yml` and can be overridden via `group_vars` or `host_vars`.

| Variable | Default | Description |
|---|---|---|
| `common_centos_packages` | `[curl, wget, git, vim-enhanced, ...]` | List of dnf packages to install |
| `common_centos_update` | `false` | Whether to run `dnf update` |
| `common_centos_auto_updates` | `true` | Enable `dnf-automatic` |
| `common_centos_timezone` | `UTC` | System timezone |
| `common_centos_set_hostname` | `false` | Set hostname from inventory name |
| `common_centos_selinux_state` | `enforcing` | SELinux state: `enforcing`, `permissive`, `disabled` |

## Tags

| Tag | Description |
|---|---|
| `common` | All tasks in this role |
| `packages` | Package installation and update |
| `update` | Package update only |
| `timezone` | Timezone configuration |
| `hostname` | Hostname configuration |
| `security` | Security-related tasks (SELinux, auto-updates) |
| `selinux` | SELinux configuration only |

## Example Usage

```yaml
# In a playbook
- hosts: centos
  become: true
  roles:
    - role: common_centos

# Override variables in group_vars/centos.yml
common_centos_timezone: "Asia/Jerusalem"
common_centos_selinux_state: "permissive"
```

## Dependencies

None.
