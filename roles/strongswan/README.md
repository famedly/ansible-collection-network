# `famedly.network.strongswan` ansible role

```yaml
vars:
  ipsec_connections:
  - name: disConnect-networks-inc
    local:
      outside_address: "1.2.3.4"
      allowed_networks:
        - "192.168.178.0/24"
    remote:
      outside_address: "2.3.4.5"
      allowed_networks:
        - "0/0"
    auth_by: psk
    auth_secret: "bottom secret"
    interface: eth0
```
