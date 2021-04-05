yggdrasil
=========

![Matrix](https://img.shields.io/matrix/ansible-yggdrasil:matrix.org)

Deploy yggdrasil using Ansible.
![Yggdrasil Documentation](https://yggdrasil-network.github.io/)

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
You should rename the generated number to what your host is called in your inventory file or if that host already has vars, append the contents of the generated files.
You want to comment out the `ansible_host` var, considering that IP won't be reachable until you've deployed it.

### Peers
There's two options for adding peers,
you can peer with other hosts from the same inventory,
or with external peers.

For peers from the same inventory, you can just put their inventory name into
an array at `yggdrasil_ansible_peers`, and set an `yggdrasil_listen_address` in the host's variables. An example would look like this:

```yaml
#host_vars/host_x/vars
yggdrasil_ansible_peers:
  - name: "foo-host"
    peering: true
  - name: "bar-host"
    peering: true
    sfw: "whitelisted"
  - name: "foobar"
    sfw: "blacklisted"
```

```yaml
#host_vars/foo-host/vars 
yggdrasil_listen_address: "tcp://99.0.0.1:61216"
#you can also use ipv6: yggdrasil_listen_address: "tcp://[{{ hosts_public_ipv6 }}]:61216"
```


For external peers, the procedure is similar. The `public_key` is optional.
Adding the public peers of the roles author would look like this:

```yaml
yggdrasil_extra_peers:
  - name: "USA/Kansas/Lenexa"
    peering:
      global: "tcp://108.175.10.127:61216"
    public_key: "d00480cf92fe09c2dbcac6b3df1846bfc2d1457f4634ede4f83892d5bdb7ad59"

  - name: "Germany/Baden-Württemberg/Baden-Baden"
    peering:
      global: "tcp://82.165.69.111:61216"
    sfw: "whitelisted"
    public_key: "946d473fd07acc8a5a0490174a34a8a11be30a2d4549e4d3013ec5f2fb40717e"

  - name: "Germany/Hessen/Frankfurt-am-Main"
    peering:
      global: "tcp://51.75.65.46:65352"
    public_key: "bf7a958893a173b9081c12bc318f60cd3a7c4989cf0435be9da7ebafc1a6ee13"
```

### Session Firewall

Yggdrasil has a in-built Session Firewall. This Firewall can prevent traffic from other yggdrasil hosts reaching the host. To activate the Firewall you have to set the variable `yggdrasil_sfw_enable: true` (default is false).

You can disable connections from directly peered hosts with `yggdrasil_sfw_allow_direct: false`
To disable traffic coming from hosts in the network who are not directly peered set `yggdrasil_sfw_allow_remote: false`
To disallow initiating Sessions to other hosts in the yggdrasil network set `yggdrasil_sfw_allow_outbound: false`. 
If this is set to true and a connection is established with another host, this host can respond even if `yggdrasil_sfw_allow_direct` and `yggdrasil_sfw_allow_remote` are set to false.

You can also Whitelist and Blacklist hosts so they can be handled differently from the general behavior. This option is set in the `yggdrasil_ansible_peers` array (see the example above).

### Other

For other available variables take a look at the `templates/yggdrasil.conf.j2`,
`vars/main.yml` and `defaults/main.yml`
Some useful config parameters might be:

```yaml
yggdrasil_node_name: "{{ var_containing_some_name }}" #node_name to announce to the yggdrasil network
yggdrasil_nodeinfo_privacy: "{{ true if group_names contains private_host_group else false }}" #if set to true the host does not announce its node_name to the yggdrasil network
yggdrasil_multicast_interfaces: [] #disable announcing the yggdrasil ip via ipv6 multicast to the connected layer 2 network
```


Example Playbook
----------------

    - hosts: servers
      roles:
         - role: yggdrasil
           become: yes
      vars:
        yggdrasil_node_name: "testvar"

License
-------

AGPLv3

Author Information
------------------

Jan Christian Grünhage `<jan.christian@gruenhage.xyz>`
