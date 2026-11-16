# Changelog

All notable changes to this deployment definition are documented in this file.
The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

### Added

- README with configuration reference, requirements and project icon.
- Icon assets (default, light and dark variants) with their sources in `assets/ICON_SOURCE.md`.

## [0.0.1]

### Added

- Initial App-as-Code service definition for the NetBird reverse proxy (`netbirdio/reverse-proxy:0.74.2`)
  with Docker Compose deployment.
- Standalone edge proxy on host ports 80 and 443 with ACME certificates in a persistent volume.
