## Tips and notes
- Improvement ideas about this playbook
  - Split into more sub playbook is better for resuming with *start-from-task* option
  - One node's deployment failure should stop other nodes from further deployment, which might lead to dirty data such etcd cluster setup.
  - Should ensure correctness of some critical deployment milestone such as *etcd up and running*.
  - There is no script to *destory* some deployment, such destory etcd.
  - Install some basic tools such as telnet, lsof, tree...

- Install vagrant-proxyconf
`vagrant plugin install vagrant-proxyconf`

- Need to turn swap off before starting playbook
Maybe that should be part of automation steps.
Even after running `swapoff -a`, *ansible_swaptotal_mb* is still not 0. The fix is to run playbook with *--flush-cache* flag.

- Need to configure *etcd_address* like *access_ip*, since that's the address that etcd will listen on for peer communication.
The default configuration will use default IPv4 address as it is configured in this way:*etcd_address: "{{ ip | default(ansible_default_ipv4['address']) }}"*

- In order to destroy etcd deployment, need to
    - Remove *etcd* docker container from each node.
    - Remove *etcd_data_dir* directory on each etcd node, which by default is */var/lib/etcd*
    - Remove *etcd_cert_dir* directory on each node, which by default is: *{{ etcd_config_dir }}/ssl*, with *etcd_config_dir* defaults to */etc/ssl/etcd*

```
docker rm -f etcd*
rm -fr /etc/ssl/etcd/ /var/lib/etcd/
```
- etcd service is too sensitive to clock, so it's better to install ntp and all nodes are better to be deployed to the same location
```
 rafthttp: the clock difference against peer 18c162296d435a07 is too high [1.013489365s > 1s]
```
