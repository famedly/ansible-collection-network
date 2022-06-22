# `famedly.network.strongswan` ansible role

a role to deploy `strongswan` and required network configuration to get running ipsec esp, tested with IPv4 and IKEv2

# Notes on usage

there are 2 ways of configuration on offer, there's `swanctl` and the legacy configuration utility known as `starter`
as this role was designed with the goal of switching everything to use `swanctl` in the future, support for new features has been added but may not fail gracefully on setups using `starter`.

for a bare-bones comparison of the keys and their defaults, see [Migration from ipsec.conf to swanctl.conf](https://wiki.strongswan.org/projects/strongswan/wiki/Fromipsecconf)

Here's a little list to warn you:
* `starter` only supports a single child entry, as it doesn't know the concept of multiple children.
* defaults will default to the values used as default by `swanctl`, these are often the opposite of `starter`

the `strongswan` documentation is pretty extensive, so it's advisable to look there frequently, along with the documentation of your peers router hardware.
manufacturers such as cisco, fortinet and f5 often implement ipsec incorrectly, but if you use juniper, arista or some box with a pfsense, opnsense or vyos, you should have no such troubles.

## Quirks of starter

```yaml
vars:
  ipsec_connections:
  - name: exmaple
    local_outside_address: "192.168.64.4/24"
    local_inside_address: "10.0.0.4/24"
    remote_outside_address: "192.168.64.3/24"
    remote_inside_address: "10.0.0.3/24"
    ike:
      auth: "secret"
      rekey_time: 15m
      version: 2
      proposals:
      - aes256-sha256-ecp384
      - default
      secret: "bottom secret"
    child:
      - 1:
        start_action: start
        dpd_action: restart
        esp_proposals:
        - aes256-sha256-ecp256
        - aes256-sha256-ecp384
        - aes256-sha256-ecp521
        - default
        life_time: 5m
        local_allowed_networks:
          - "10.0.0.0/24"
        remote_allowed_networks:
          - "10.0.0.0/24"
    interface: enp0s6
```
