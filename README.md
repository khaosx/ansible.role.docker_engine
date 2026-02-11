# ansible.role.docker-engine

Install and configure Docker Engine on Ubuntu:

- Adds Docker's upstream APT repository and installs `docker-ce` + related packages
- Manages `/etc/docker/daemon.json` (optional, enabled by default in this role)
- Ensures the `docker` group exists with a fixed GID (defaults to `3000`) and adds nominated users to it
- Ensures Docker networks exist
- Creates a canonical stacks directory (defaults to `/opt/stacks`)
- Deploys a small `docker-tools` stack (currently Dozzle) from a rendered compose file under `/opt/stacks/docker-tools`

## Requirements

- Ubuntu host
- Play/role inclusion must run with `become: true`
- Ansible collection: `community.docker`

## Role Variables

Defaults live in `defaults/main.yml`.

- `docker_engine_packages`: list of packages installed
- `docker_engine_group`: docker group name (default: `docker`)
- `docker_engine_gid`: docker group GID (default: `3000`)
- `docker_engine_users`: users added to the docker group (default includes `jarvis`)
- `docker_engine_manage_daemon_json`: whether to manage `/etc/docker/daemon.json`
- `docker_engine_daemon_config`: daemon.json content (rendered as JSON)
- `docker_engine_networks`: docker networks to create
- `docker_engine_stacks_dir`: stacks root directory (default: `/opt/stacks`)

docker-tools stack (Dozzle):

- `docker_engine_stack_name`: stack name (default: `docker-tools`)
- `docker_engine_dozzle_image`: Dozzle image (default: `amir20/dozzle:latest`)
- `docker_engine_dozzle_container_name`: container name (default: `dozzle`)
- `docker_engine_dozzle_restart`: restart policy (default: `unless-stopped`)
- `docker_engine_dozzle_port`: host port mapping to container `8080` (default: `9081`)
- `docker_engine_dozzle_enable_actions`: enable Dozzle actions (default: `true`)
- `docker_engine_dozzle_enable_shell`: enable Dozzle shell (default: `true`)

## Example Playbook

```yaml
---
- name: Docker Engine
  hosts: docker
  become: true
  roles:
    - role: docker_engine
```

## What Gets Deployed

After a run, you should have:

- Docker Engine installed and running (`docker` service enabled + started)
- Optional daemon config at `/etc/docker/daemon.json`
- Networks defined in `docker_engine_networks`
- A rendered compose file at `/opt/stacks/docker-tools/docker-compose.yml`
- A `docker compose` project named `docker-tools` (includes a `dozzle` container listening on `9081`)

## License

MIT. See `LICENSE`.

