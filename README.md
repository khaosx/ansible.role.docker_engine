# Role: docker_engine

## Description

Installs and configures Docker Engine on Debian/Ubuntu hosts, optionally manages `/etc/docker/daemon.json`, ensures required Docker networks exist, applies UFW profile rules for Dozzle, and deploys a docker-tools compose stack.

## Requirements

- Ansible >= 2.14
- Target OS: Ubuntu, Debian
- `community.docker` collection available
- `ufw_profiles` role available when `docker_engine_ufw_allow_applications` is non-empty

## Privilege Escalation

requires_become: true

## Role Variables

| Variable | Default | Required | Description |
|---|---|---|---|
| `docker_engine_old_docker` | see defaults | No | Legacy Docker packages removed before install. |
| `docker_engine_apt_key_url` | `https://download.docker.com/linux/ubuntu/gpg` | No | Docker repository signing key URL. |
| `docker_engine_arch_map` | see defaults | No | Mapping of host architecture names to Docker apt architecture values. |
| `docker_engine_repo_arch` | derived | No | Docker apt architecture used in repository stanza. |
| `docker_engine_packages` | see defaults | No | Docker and helper packages to install. |
| `docker_engine_stacks_dir` | `/opt/stacks` | No | Root directory for stack deployments. |
| `docker_engine_group` | `docker` | No | Docker group name. |
| `docker_engine_gid` | `3000` | No | Docker group GID. |
| `docker_engine_users` | `['jarvis']` | No | Users added to Docker group. |
| `docker_engine_manage_daemon_json` | `true` | No | Enable management of `/etc/docker/daemon.json`. |
| `docker_engine_daemon_config` | see defaults | No | Daemon config rendered to JSON. |
| `docker_engine_networks` | see defaults | No | Docker networks to enforce. |
| `docker_engine_stack_name` | `docker-tools` | No | Compose project name. |
| `docker_engine_dozzle_image` | `amir20/dozzle:latest` | No | Dozzle image reference. |
| `docker_engine_dozzle_container_name` | `dozzle` | No | Dozzle container name. |
| `docker_engine_dozzle_restart` | `unless-stopped` | No | Dozzle restart policy. |
| `docker_engine_dozzle_port` | `9081` | No | Host port mapped to Dozzle `8080`. |
| `docker_engine_dozzle_enable_actions` | `true` | No | Enable Dozzle actions. |
| `docker_engine_dozzle_enable_shell` | `true` | No | Enable Dozzle shell. |
| `docker_engine_ufw_profiles_catalog` | see defaults | No | UFW profile catalog sent to `ufw_profiles`. |
| `docker_engine_ufw_allow_applications` | `['Dozzle']` | No | UFW applications allowed via `ufw_profiles`. |

## Outbound Artifacts

- Docker packages and service state (`docker` systemd unit)
- Optional `/etc/docker/daemon.json`
- Docker networks from `docker_engine_networks`
- Stack directory at `{{ docker_engine_stacks_dir }}/{{ docker_engine_stack_name }}`
- Compose file at `{{ docker_engine_stacks_dir }}/{{ docker_engine_stack_name }}/docker-compose.yml`

## Dependencies

- Runtime role inclusion of `ufw_profiles` when firewall application list is non-empty

## Capabilities

- Installs Docker Engine from Docker upstream apt repository
- Removes legacy Docker packages before installation
- Configures Docker daemon JSON and restarts service when changed
- Ensures Docker networks exist
- Deploys Dozzle via Docker Compose v2
- Applies UFW application profile rules for Dozzle

## Idempotency

idempotent: true

## Atomic

atomic: false

## Rollback

No automated rollback is provided. Template tasks use `backup: true` for manual rollback support.

## Required Credentials or Privileges

- Root privilege escalation (`become: true`)

## Check Mode Behavior

No tasks are explicitly skipped in check mode.

## Supported Platforms

- Ubuntu
- Debian

## Example Playbook

```yaml
---
- name: Configure Docker hosts
  hosts: docker_hosts
  gather_facts: false
  become: true
  roles:
    - role: khaosx.homelab.docker_engine
```

## License

MIT

## Author

khaosx
