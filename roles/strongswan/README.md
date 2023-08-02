# `famedly.network.strongswan` ansible role

## Notes on usage

this role supports ***only*** the more modern strongswan configuration utility - `swanctl` - as `stroke`-based configuration is deprecated and will be removed in the upcoming 6.0 release.

the `strongswan` documentation is pretty extensive, so it's advisable to look there frequently, along with the documentation of your peers router hardware.
manufacturers such as cisco, fortinet and f5 often implement take some liberties implementing the spec.

## Features of this role

this allows you to specify `n` connections per list entry in `strongswan_swanctl_config, the list item's name will be used as the directory name for the configuration files.

you can use `__extend__` anywhere in your config. the value will be used as a key relative to swanctl's root namespace.

```yaml
strongswan_swanctl_config:
  connections:
    "example":
      __extend__: default-connection
      remote:
        id: peer.your.example

  default_connection:
    local:
      id: "{{ ansible_hostname }}"
```

will be rendered to

```
connections {
  example : default-connection {
    remote {
      id = peer.your.example
    }
  }
}
default-connection {
  local {
    id = you.your.example
  }
}
```

which is functionally equivivalent to

```
connections {
  example {
    local {
      id = you.your.example
    }
    remote {
      id = peer.your.example
    }
  }
}
```

## Example Configuration
```yaml
vars:
  strongswan_swanctl_config:
  - "evlli":
    secrets:
    - "famedly-evlli":
      id:
        - "{{ ansible_hostname }}"
        - "route.f6.evl.li"
      secret: "miaumiauawooooo!bark"
      type: ike

    connections:
    - "famedly-evlli":
      __extend__: [default-connection]
      local_addrs: [10.0.0.1]
      remote_addrs: ["route.f6.evl.li"]
      version: 2
      rekey_time: 15m
      proposals:
        - aes256-sha256-ecp384
      remote:
        auth: psk
        id: "route.f6.evl.li"
      child:
        - "devops-evlli-wildcard":
          __extend__: [default-child]
          remote_ts:
            ::/0
          local_ts:
            0.0/0
          start_action: start
          close_action: route
          dpd_action: restart
          esp_proposals:
          - aes256-sha256-ecp256
          - aes256-sha256-ecp384
          - aes256-sha256-ecp521
          life_time: 5m
```

## Authors
Evelyn Alicke <e.alicke@famedly.com>
