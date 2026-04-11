# k3s

Installs a K3s server (single-node lightweight Kubernetes) using the official install script.

Equivalent to running:

```bash
curl -sfL https://get.k3s.io | sh -
```

## Requirements

- Internet access to download K3s
- Collections: none (uses builtin modules only)

## Role Variables

All variables are defined in `defaults/main.yml` and can be overridden via `group_vars` or `host_vars`.

| Variable | Default | Description |
|---|---|---|
| `k3s_version` | `""` (latest) | Specific K3s version to install |
| `k3s_channel` | `stable` | Install channel: `stable`, `latest`, `testing` |
| `k3s_extra_args` | `""` | Additional k3s server arguments (e.g. `--disable traefik`) |
| `k3s_kubeconfig_mode` | `0644` | Kubeconfig file permissions |
| `k3s_kubectl_symlink` | `true` | Create `/usr/local/bin/kubectl` symlink |

## Example Usage

```yaml
# In a playbook
- hosts: k3s
  become: true
  roles:
    - role: k3s

# Override variables in group_vars/k3s.yml
k3s_version: "v1.31.4+k3s1"
k3s_extra_args: "--disable traefik --tls-san 10.0.1.100"
```

## Dependencies

None.
