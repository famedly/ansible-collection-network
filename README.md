# `famedly.network` ansible collection

![Matrix](https://img.shields.io/matrix/ansible-famedly:matrix.org)

## Scope

This ansible collection contains roles for various network-related services, like
wireguard as VPN software or a reverse-proxy like traefik.

## Roles

- [`roles/wireguard`](roles/wireguard/README.md): can install and configure
  wireguard,  a versatile and modern, cryptographically strong, VPN service.
- [`roles/traefik`](roles/traefik/README.md): a modern reverse proxy with a
  built-in ACME client and autodiscovery for docker containers

## License

All roles are licenced AGPLv3.

## Author Information

- Jan Christian Grünhage <jan.christian@gruenhage.xyz>
- Johanna Dorothea Reichmann <transcaffeine@finallycoffee.eu>
