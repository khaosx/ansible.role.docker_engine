# Changelog

## [Unreleased]

- Added stylebook-compliant role structure with `tasks/set_vars.yml`, `vars/main.yml`, and platform vars.
- Added `meta/argument_specs.yml` covering all role defaults.
- Refactored task and handler naming to `Area | Action` pattern.
- Switched Docker service management from `service` to `systemd`.
- Replaced inline daemon.json `copy` content with a managed template file.
- Added `ansible_managed` header to compose template.
- Updated role README to full stylebook contract format.
