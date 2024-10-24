# `famedly.network.wireguard` ansible role

This ansible role can be used to set up [wireguard](https://www.wireguard.com/)
on multiple ansible hosts to create (for example) meshed VPN networks on them.

The role assumes that for setting up a new wireguard network, all participating hosts are 
available during the execution time of the playbook. This is needed to distribute the public 
wireguard keys correctly.

As soon as the tunnel has initally been set up, you might want to add the parameters used for 
the participating hosts to `inventory/group_vars/all/wireguard.yml` as dictionaries below the 
variable `wireguard_config_directory`, f. ex. (for two peers called `alice` and `bob`):

```yaml
wireguard_config_directory:
  alice.famedly.de:
    wg_net0:
      wg_pubkey: "<alice's public key of interface 'wg_net0'>"
      ip_addresses_cidr:
      - "<IP address with CIDR>"
      peering_ip: "bob.famedly.de"
      peering_port: 51820
  bob.famedly.de:
    wg_net0:
      wg_pubkey: "<bob's public key of interface 'wg_net0'>"
      ip_addresses_cidr:
      - "<IP address with CIDR>"
      peering_ip: "alice.famedly.de"
      peering_port: 51820
```

As soon as these variables have been correctly set up, the role even works without 
all of the participating hosts being included when running the playbook, because all 
of the needed information is available inside the `group_vars/all` directory.

## Usage

Almost all configuration is done by populating the `wg_ifaces` variable per
host. The `iface_name` serves as both the interface name and as the unique
(global) slug of the connection - when setting the same `iface_name` on two
hosts with IPs in the same subnet, the role will create a wireguard network
between them.

See the following example for a dead-simple wireguard network:

```yaml
# host_vars/hostA/vars.yml
wg_ifaces:
  - iface_name: wg_net0
    ip_addresses_cidr:
      - 192.168.42.1/30
    peering_ip: "{{ public_ip }}"
    peering_port: 51820
    peers: [ hostB ]
# host_vars/hostB/vars.yml
wg_ifaces:
  - iface_name: wg_net0
    ip_addresses_cidr:
      - 192.168.42.2/30
    peering_ip: "{{ public_ip }}"
    peering_port: 51820
    peers: [ hostA ]
```

For `ip_addresses_cidr`, multiple IPs (in CIDR) notation are allowed.
As they are handled as strings, you can (and should) use IPv6 aswell.

Please note that the encoded subnet is used as the `AllowedIPs` field
on the remote peer. This _can_ lead to allowing more traffic to a host
than intended. This behaviour may change in the future.

## Supported Topologies

### Mesh

Assuming you have an ansible group of hosts called `prod`,
and you want to setup a full mesh on them. Their public IPs
are already populated in `server_public_ip`.

```yaml
# group_vars/prod/wireguard.yml
# Create a unique index per host for the IP
index: "{{ lookup('ansible.utils.index_of', groups.prod | sort, 'eq', inventory_hostname) | int + 1 }}"
# Configure the interface `prodnet`
wg_ifaces:
  - iface_name: prodnet
    ip_addresses_cidr: [ "10.42.20.{{ index }}/24" ]
    peering_ip: "{{ server_public_ip }}"
    peering_port: 51820
    peers: "{{ groups.prod | reject('eq', inventory_hostname) }}"
# The above line prevents peering of hosts with themselves
```

### Star

Assuming you have an ansible group of hosts called `clients`,
and you want to setup all of them to peer with `my_server`.
Their public IPs are all already populated in `server_public_ip`,
and `my_server` is not in the `clients` ansible group.

```yaml
# host_vars/my_server/wireguard.yml
wg_ifaces:
  - iface_name: starnet
    ip_addresses_cidr:
      - "fd00:feed:c0ff:ee::1/96"
      - "10.42.20.1/24"
    peering_ip: "{{ server_public_ip }}"
    peering_port: 51825
    peers: "{{ groups.clients }}"
```

```yaml
# group_vars/clients/wireguard.yml
index: "{{ lookup('ansible.utils.index_of', groups.clients | sort, 'eq', inventory_hostname) | int + 2 }}"
wg_ifaces:
  - iface_name: starnet
    ip_addresses_cidr:
      - "fd00:feed:c0ff:ee::{{ '%#x' % index }}/96"
      - "10.42.20.{{ index }}/24"
    peering_ip: "{{ server_public_ip }}"
    peering_port: 51825
    peers: [ my_server ]
```

## Roadmap

- [ ] Allow configuring `AllowedIPs` to route all traffic through wireguard using a default route
- [ ] Allow using a peer as DNS server
- [ ] Add `PresharedKey` distribution for post-quantum cryptography
