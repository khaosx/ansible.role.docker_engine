# ansible.role.docker-engine

Install and configure Docker Engine on Ubuntu hosts, then deploy a `docker-tools` compose stack under `/opt/stacks`.

## Purpose

This role:
- Installs Docker Engine from Docker's upstream APT repo
- Manages the Docker group (GID `3000`) and member users
- Optionally manages `/etc/docker/daemon.json`
- Ensures required Docker networks exist
- Renders and deploys the `docker-tools` compose project

## Requirements

- Ubuntu host
- `become: true` at play/role inclusion level
- `community.docker` collection available

## Role Execution Model

This role is intended to run subordinately from a controlling playbook.

The controlling playbook should own:
- Baseline host provisioning
- Shared inventory and vault structure
- Global site variables
- Role ordering and privilege model

## Control Playbook Contract

Defaults for this role are in `defaults/main.yml`.
Controller inventory/vault can override these values as needed.

### Expected upstream variables

| Variable | Required | Description |
|---|---|---|
| `docker_engine_users` | no | Users to add to the Docker group (default includes `jarvis`) |
| `docker_engine_manage_daemon_json` | no | Toggle role management of `/etc/docker/daemon.json` |
| `docker_engine_daemon_config` | no | Docker daemon config map rendered to JSON |
| `docker_engine_networks` | no | Docker networks to enforce |
| `docker_engine_stacks_dir` | no | Stack root directory (default `/opt/stacks`) |

### Variable naming convention

Role-owned variables use the `docker_engine_` prefix.

## Key Variables

- `docker_engine_group`: group name (default `docker`)
- `docker_engine_gid`: fixed GID (default `3000`)
- `docker_engine_packages`: Docker and supporting packages
- `docker_engine_stack_name`: compose project name (default `docker-tools`)
- `docker_engine_dozzle_image`: Dozzle image
- `docker_engine_dozzle_port`: host port mapped to container `8080`

## Example Controller Play

```yaml
---
- name: Docker hosts
  hosts: docker
  become: true
  gather_facts: true
  roles:
    - role: docker_engine
```

## Resulting Host State

After a successful run:
- Docker packages are installed
- Docker service is enabled and running
- Optional daemon config exists at `/etc/docker/daemon.json`
- Declared Docker networks are present
- `/opt/stacks/docker-tools/docker-compose.yml` is rendered
- `docker compose` project `docker-tools` is applied

## License

MIT
