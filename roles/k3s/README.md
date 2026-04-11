# k3s

Installs and configures a K3s lightweight Kubernetes cluster.

## Requirements

- Target must be a Debian or RedHat based system
- Internet access to download the K3s install script
- Collections: none (uses builtin modules only)

## Role Variables

All variables are defined in `defaults/main.yml` and can be overridden via `group_vars` or `host_vars`.

| Variable | Default | Description |
|---|---|---|
| `k3s_version` | `""` (latest) | Specific K3s version to install |
| `k3s_channel` | `stable` | Install channel: `stable`, `latest`, `testing` |
| `k3s_node_type` | `server` | Node type: `server` or `agent` |
| `k3s_server_extra_args` | `""` | Additional server arguments |
| `k3s_agent_extra_args` | `""` | Additional agent arguments |
| `k3s_kubectl_symlink` | `true` | Create `/usr/local/bin/kubectl` symlink |
| `k3s_kubeconfig_mode` | `0644` | Kubeconfig file permissions |
| `k3s_write_kubeconfig_mode` | `0644` | `--write-kubeconfig-mode` argument |
| `k3s_disable_components` | `[]` | Components to disable (e.g., `traefik`, `servicelb`) |
| `k3s_data_dir` | `/var/lib/rancher/k3s` | K3s data directory |

## Tags

| Tag | Description |
|---|---|
| `k3s` | All tasks in this role |
| `preflight` | Pre-installation validation checks |
| `install` | K3s binary installation |
| `configure` | Post-install configuration |

## Task Files

| File | Description |
|---|---|
| `main.yml` | Entry point — orchestrates all task includes |
| `preflight.yml` | OS validation, existing install detection |
| `install_server.yml` | Downloads and runs the K3s install script |
| `configure.yml` | kubectl symlink, kubeconfig permissions |

## Example Usage

```yaml
# In a playbook
- hosts: k3s
  become: true
  roles:
    - role: k3s

# Override variables in group_vars/k3s.yml
k3s_version: "v1.31.4+k3s1"
k3s_disable_components:
  - traefik
k3s_server_extra_args: "--tls-san 10.0.1.100"

# Per-host overrides in host_vars or inventory
# For agent nodes:
k3s_node_type: agent
k3s_server_url: "https://10.0.1.20:6443"
```

## Dependencies

None.
