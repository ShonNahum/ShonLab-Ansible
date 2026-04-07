# ShonLab Ansible

A modular, production-ready Ansible project for provisioning Ubuntu, CentOS/RHEL, and K3s Kubernetes clusters.

## Project Structure

```
.
├── ansible.cfg                    # Ansible configuration
├── site.yml                       # Main playbook entrypoint
├── requirements.yml               # Galaxy dependencies
├── inventories/
│   └── dev/
│       ├── hosts.ini              # Inventory hosts
│       ├── group_vars/
│       │   ├── all.yml            # Variables for all hosts
│       │   └── k3s.yml            # Variables for k3s group
│       └── host_vars/             # Per-host variable overrides
└── roles/
    ├── common_ubuntu/             # Ubuntu base configuration
    │   ├── defaults/main.yml
    │   ├── tasks/main.yml
    │   ├── handlers/main.yml
    │   └── meta/main.yml
    ├── common_centos/             # CentOS/RHEL base configuration
    │   ├── defaults/main.yml
    │   ├── tasks/main.yml
    │   ├── handlers/main.yml
    │   └── meta/main.yml
    └── k3s/                       # K3s Kubernetes cluster
        ├── defaults/main.yml
        ├── tasks/
        │   ├── main.yml
        │   ├── preflight.yml
        │   ├── install_server.yml
        │   └── configure.yml
        ├── handlers/main.yml
        └── meta/main.yml
```

## Prerequisites

- Ansible >= 2.14
- SSH access to target hosts
- Python 3 on target hosts

## Quick Start

### 1. Configure Inventory

Edit `inventories/dev/hosts.ini` and uncomment/add your hosts:

```ini
[ubuntu]
ubuntu-01 ansible_host=192.168.1.10

[centos]
centos-01 ansible_host=192.168.1.20
```

### 2. Customize Variables

Override role defaults via group_vars or host_vars:

```yaml
# inventories/dev/group_vars/all.yml
ntp_timezone: "America/New_York"
```

### 3. Run the Playbook

```bash
# Run everything
ansible-playbook site.yml

# Run against a specific inventory
ansible-playbook -i inventories/dev/hosts.ini site.yml

# Run only specific roles using tags
ansible-playbook site.yml --tags packages

# Target specific hosts
ansible-playbook site.yml --limit ubuntu

# Dry run (check mode)
ansible-playbook site.yml --check --diff
```

## Roles

### `common_ubuntu`

Installs and configures baseline packages on Ubuntu systems.

| Variable | Default | Description |
|---|---|---|
| `common_ubuntu_packages` | `[curl, wget, git, vim, ...]` | List of apt packages to install |
| `common_ubuntu_upgrade` | `false` | Whether to run apt upgrade |
| `common_ubuntu_upgrade_type` | `safe` | Upgrade type (dist, full, safe) |
| `common_ubuntu_auto_updates` | `true` | Enable unattended-upgrades |
| `common_ubuntu_timezone` | `UTC` | System timezone |
| `common_ubuntu_set_hostname` | `false` | Set hostname from inventory |

### `common_centos`

Installs and configures baseline packages on CentOS/RHEL systems.

| Variable | Default | Description |
|---|---|---|
| `common_centos_packages` | `[curl, wget, git, vim-enhanced, ...]` | List of dnf packages to install |
| `common_centos_update` | `false` | Whether to run dnf update |
| `common_centos_auto_updates` | `true` | Enable dnf-automatic |
| `common_centos_timezone` | `UTC` | System timezone |
| `common_centos_set_hostname` | `false` | Set hostname from inventory |
| `common_centos_selinux_state` | `enforcing` | SELinux state |

### `k3s`

Installs and configures a K3s lightweight Kubernetes cluster.

| Variable | Default | Description |
|---|---|---|
| `k3s_version` | `""` (latest) | Specific K3s version |
| `k3s_channel` | `stable` | Install channel |
| `k3s_node_type` | `server` | Node type (server) |
| `k3s_server_extra_args` | `""` | Additional server arguments |
| `k3s_kubectl_symlink` | `true` | Create kubectl symlink |
| `k3s_kubeconfig_mode` | `0644` | Kubeconfig file permissions |
| `k3s_disable_components` | `[]` | Components to disable (e.g., traefik) |
| `k3s_data_dir` | `/var/lib/rancher/k3s` | K3s data directory |

## Adding New Environments

Copy the `inventories/dev/` directory:

```bash
cp -r inventories/dev inventories/staging
```

Then update `hosts.ini` and `group_vars/` for the new environment and run:

```bash
ansible-playbook -i inventories/staging/hosts.ini site.yml
```

## Extending with New Roles

```bash
# Create a new role skeleton
ansible-galaxy init roles/my_new_role

# Add it to site.yml
```

## License

MIT
