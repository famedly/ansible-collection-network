# `famedly.network` ansible collection

![Matrix](https://img.shields.io/matrix/ansible-famedly:matrix.org)

## Scope

This ansible collection contains roles for various network-related services, like
yggdrasil and wireguard as VPN/mesh-networking software or a reverse-proxy like traefik.

## Roles

- [`roles/yggdrasil`](roles/yggdrasil/README.md): for installing and configuring
  the yggdrasil IPv6 mesh overlay network.
- [`roles/wireguard`](roles/wireguard/README.md): can install and configure
  wireguard,  a versatile and modern, cryptographically strong, VPN service.
- [`roles/traefik`](roles/traefik/README.md): a modern reverse proxy with a
  built-in ACME client and autodiscovery for docker containers

## License

All roles excpet systemd-networkd are licenced AGPLv3.
The systemd-networkd role is a fork from [aruhier](https://github.com/aruhier/ansible-role-systemd-networkd) and therefor licensed under the BSD-2-Clause license.

## Author Information

- Jan Christian Grünhage <jan.christian@gruenhage.xyz>
- Johanna Dorothea Reichmann <transcaffeine@finallycoffee.eu>
