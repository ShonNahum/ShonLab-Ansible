# ShonLab Ansible

A modular Ansible project for provisioning Ubuntu, CentOS/RHEL That i use on my HomeLab.

## Project Structure

```
ShonLab-Ansible/
├── ansible.cfg                             # Ansible configuration
├── collections/
│   └── requirements.yml                    # Galaxy collections (AWX auto-installs)
├── inventories/
│   ├── dev/                                # Development environment
│   │   ├── hosts.yml                       # Inventory hosts
│   │   ├── group_vars/
│   │   │   ├── all.yml                     # Variables for all hosts
│   │   │   ├── ubuntu.yml                  # Ubuntu group overrides
│   │   │   ├── centos.yml                  # CentOS group overrides
│   │   │   └── k3s.yml                     # K3s group overrides
│   │   └── host_vars/                      # Per-host variable overrides
├── playbooks/                              # AWX Job Template entry points
│   ├── common_ubuntu.yml                   # Ubuntu baseline configuration
│   ├── common_centos.yml                   # CentOS baseline configuration
│   └── deploy_k3s.yml                      # K3s cluster deployment
├── roles/
│   ├── requirements.yml                    # External Galaxy roles
│   ├── common_ubuntu/                      # Ubuntu base configuration role
│   ├── common_centos/                      # CentOS/RHEL base configuration role
│   └── k3s/                                # K3s Kubernetes cluster role
├── .ansible-lint                           # Ansible Lint configuration
├── .yamllint                               # YAML Lint configuration
└── .gitignore
```

## Prerequisites

- Ansible >= 2.14
- SSH access to target hosts
- Python 3 on target hosts
- Required collections (auto-installed by AWX):
  - `community.general`
  - `ansible.posix`

## Quick Start (CLI)

### 1. Install Dependencies

```bash
# Install required collections
ansible-galaxy collection install -r collections/requirements.yml

# Install external roles (if any)
ansible-galaxy role install -r roles/requirements.yml
```

### 2. Configure Inventory

Edit `inventories/dev/hosts.yml` and add your hosts:

```yaml
all:
  children:
    ubuntu:
      hosts:
        ubuntu-01:
          ansible_host: 192.168.1.10
    centos:
      hosts:
        centos-01:
          ansible_host: 192.168.1.20
```

### 3. Customize Variables

Override role defaults in `group_vars/` or `host_vars/`:

```yaml
# inventories/dev/group_vars/all.yml
ntp_timezone: "Asia/Jerusalem"
```

### 4. Run Playbooks

```bash
# Ubuntu baseline
ansible-playbook -i inventories/dev/hosts.yml playbooks/common_ubuntu.yml

# CentOS baseline
ansible-playbook -i inventories/dev/hosts.yml playbooks/common_centos.yml

# K3s deployment
ansible-playbook -i inventories/dev/hosts.yml playbooks/deploy_k3s.yml

# Dry run (check mode)
ansible-playbook -i inventories/dev/hosts.yml playbooks/common_ubuntu.yml --check --diff

# Run only specific tags
ansible-playbook -i inventories/dev/hosts.yml playbooks/common_centos.yml --tags packages

# Target specific hosts
ansible-playbook -i inventories/dev/hosts.yml playbooks/deploy_k3s.yml --limit centos-01
```

## Roles

Each role has its own `README.md` with full variable documentation:

- [`common_ubuntu`](roles/common_ubuntu/README.md) — Ubuntu baseline packages, timezone, hostname, auto-updates
- [`common_centos`](roles/common_centos/README.md) — CentOS baseline packages, timezone, hostname, SELinux, auto-updates
- [`k3s`](roles/k3s/README.md) — K3s server installation (single-node Kubernetes)

## Adding New Environments

Copy an existing environment directory:

```bash
cp -r inventories/dev inventories/staging
```

Edit `inventories/staging/hosts.yml` and `group_vars/` for the new environment, then run:

```bash
ansible-playbook -i inventories/staging/hosts.yml playbooks/common_ubuntu.yml
```

In AWX, create a new inventory pointing to `inventories/staging/hosts.yml`.

## Adding New Roles

```bash
# Create a new role skeleton
cd roles && ansible-galaxy init my_new_role && cd ..

# Create a playbook for the role
cat > playbooks/my_new_role.yml << 'EOF'
---
- name: Apply my new role
  hosts: target_group
  become: true
  gather_facts: true

  roles:
    - role: my_new_role
EOF
```

Then create a corresponding AWX Job Template.

## Linting

```bash
# YAML lint
yamllint .

# Ansible lint
ansible-lint

# Syntax check individual playbooks
ansible-playbook --syntax-check playbooks/common_ubuntu.yml
```

## License

MIT
