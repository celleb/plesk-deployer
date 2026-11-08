# Changelog

## v1.3 - 2026-11-08

### Added

- `ship-node-modules` input: installs production dependencies on the runner, uploads them and swaps them in on the server, for servers with too little memory for `npm ci`.

## v1.2 - 2024-12-20

### Changed

- Updated the default remote directory to `/` (root) instead of `/httpdocs` which is public by default.

## v1.1 - 2024-11-08

### Added

- Support for choosing the package manager (`npm` or `yarn`) via the `package-manager` input.

### Changed

- Updated example workflows in the README to use the `@main` branch for the latest version of the action.

## v1 - 2024-10-20

### Added

- Initial release of the Plesk Deployer Action.
