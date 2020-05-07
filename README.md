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
There are 4 variables that need to exist for the crypto part of yggdrasil:
 - `yggdrasil_encryption_public_key`
 - `yggdrasil_encryption_private_key`
 - `yggdrasil_signing_public_key`
 - `yggdrasil_signing_private_key`

You can generate keys by running the `genkeys` binary from `misc/genkeys` in this repository
in your inventory directory (the directory where you have your `host_vars` and `group_vars` directories,
see [Ansibles docs on directory layouts](https://docs.ansible.com/ansible/latest/user_guide/playbooks_best_practices.html#directory-layout) for more info).
This will generate the files `host_vars/1/vars` and `host_vars/1/vault` containing your keys and
the yggdrasil IP generated from your encryption public key.

It's recommended to use `ansible-vault` to encrypt the `vault` file.
You should rename the generated number to what your host is called in your inventory file,
and comment out the `ansible_host` var, considering that IP won't be reachable until you've deployed it.

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
Adding the public peers of the roles author would look like this:

```yaml
yggdrasil_extra_peers:
  - listen_uri: "tcp://108.175.10.127:61216" # USA/Kansas/Lenexa
    public_key: "d00480cf92fe09c2dbcac6b3df1846bfc2d1457f4634ede4f83892d5bdb7ad59"

  - listen_uri: "tcp://82.165.69.111:61216"  # Germany/Baden-Württemberg/Baden-Baden
    public_key: "946d473fd07acc8a5a0490174a34a8a11be30a2d4549e4d3013ec5f2fb40717e"

  - listen_uri: "tcp://51.75.65.46:65352"    # Germany/Hessen/Frankfurt-am-Main
    public_key: "bf7a958893a173b9081c12bc318f60cd3a7c4989cf0435be9da7ebafc1a6ee13"
```

### Other

For other available variables take a look at the `templates/yggdrasil.conf.j2`,
`vars/main.yml` and `defaults/main.yml`


Example Playbook
----------------

    - hosts: servers
      roles:
         - role: yggdrasil
           become: yes

License
-------

AGPLv3

Author Information
------------------

Jan Christian Grünhage `<jan.christian@gruenhage.xyz>`
