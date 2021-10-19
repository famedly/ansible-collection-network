# `famedly.network` ansible collection

## Scope

This ansible collection contains roles for various network-related services, like
yggdrasil and wireguard as VPN/mesh-networking software or a reverse-proxy like traefik.

## Roles

- [`roles/yggdrasil`](roles/yggdrasil/README.md): for installing and configuring
  the yggdrasil IPv6 mesh overlay network. ![Matrix](https://img.shields.io/matrix/ansible-yggdrasil:matrix.org)
- [`roles/wireguard`](roles/wireguard/README.md): can install and configure
  wireguard,  a versatile and modern, cryptographically strong, VPN service.
- [`roles/traefik`](roles/traefik/README.md): a modern reverse proxy with a
  built-in ACME client and autodiscovery for docker containers

## License

AGPLv3

## Author Information

- Jan Christian Grünhage <jan.christian@gruenhage.xyz>
- Johanna Dorothea Reichmann <transcaffeine@finallycoffee.eu>
