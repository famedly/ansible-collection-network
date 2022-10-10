yggdrasil
=========

![Matrix](https://img.shields.io/matrix/ansible-yggdrasil:matrix.org)

Deploy yggdrasil using Ansible.

Dependencies
------------

`ansible_lsb` needs additional packages on some systems,
those need to be available


Role Variables
--------------

For a very basic and barebones setup you need 2 things:

### Keys
There are 2 variables that need to exist for the crypto part of yggdrasil:
 - `yggdrasil_public_key`
 - `yggdrasil_private_key`

You can generate keys with [yggdrasil-keygen](https://gitlab.com/famedly/infra/tools/yggdrasil-keygen) for example, or you could use the key generation provided by upstream alternatively. In the end, the only relevant thing is that those two keys are present in the variables of the host you are deployng.

### Peers
There's two options for adding peers,
you can peer with other hosts from the same inventory,
or with external peers.

For peers from the same inventory, you can just put their inventory name into
an array at `yggdrasil_ansible_peers`, and set an `yggdrasil_listen_address` in the it's variables. An example would look like this:

```yaml
yggdrasil_ansible_peers:
  - another
  - host
  - and
  - some
  - more
  - hosts
```

For external peers, the procedure is similar.

```yaml
yggdrasil_extra_peers:
  alice:
    key: "0000db052493be29234341d83fbfcca3069ef47d550873690cd71113613957dc"
    peering:
      # For regular peers
      global: "tcp://1.2.3.4:61216"
  bob:
    key: "0000f4b0bf8d5c8c84701449bd72d688ecce5af106c48ba76283293d5bae5231"
    peering:
      # For peering via specific interfaces
      interfaces:
        eth0: "tls://9.8.7.6:61216"
```

### Other

For other available variables take a look at the `templates/yggdrasil.conf.j2`,
`vars/main.yml` and `defaults/main.yml`


Example Playbook
----------------
```yaml
- hosts: servers
  roles:
     - role: yggdrasil
       become: yes
```

License
-------

AGPLv3

Author Information
------------------

Jan Christian Grünhage `<jan.christian@gruenhage.xyz>`
