# Patch-Openshift-3.6
This document describes about how to do Openshift patching on 3.6 version. However, it is better to refer the Redhat technical documentation for any changes in the steps.

```
Patching the Openshift Cluster

To verify the environment before proceeding.

ansible-playbook -i hosts /usr/share/ansible/openshift-ansible/playbooks/byo/openshift_facts.yml -b -v

Take a backup of /usr/share/ansible

# cd /etc/origin/master
# tar cf /tmp/certs-and-keys-$(hostname).tar *.key *.crt

# sudo systemctl stop etcd
# source /etc/etcd/etcd.conf
# etcdctl backup --data-dir $ETCD_DATA_DIR --backup-dir $ETCD_DATA_DIR.bak
# cp "$ETCD_DATA_DIR"/member/snap/db "$ETCD_DATA_DIR.bak"/member/snap/db'
# sudo systemctl start etcd

# cd /etc/docker/certs.d/
# tar cf /tmp/docker-registry-certs-$(hostname).tar *

install latest ansible playbook
yum -y update openshift-ansible-playbooks

oc get node --show-labels

match the labels corresponding to oc get nodes --show-labels output.

ansible-playbook -i hosts /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/upgrades/v3_6/upgrade_control_plane.yml --become


Need to add /usr/sbin/restorecon in /usr/share/ansible/openshift-ansible/roles/openshift_node_upgrade/tasks/main.yml file


ansible-playbook -i hosts /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/upgrades/v3_6/upgrade_nodes.yml -e openshift_upgrade_nodes_label=region=primary -e openshift_upgrade_nodes_serial="1" --become

ansible-playbook -i hosts /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/upgrades/v3_6/upgrade_nodes.yml -e openshift_upgrade_nodes_label="region=infra,router=public" -e openshift_upgrade_nodes_serial="1" --become

ansible-playbook -i hosts /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/upgrades/v3_6/upgrade_nodes.yml -e openshift_upgrade_nodes_label="region=infra,registry=true,router=private" -e openshift_upgrade_nodes_serial="1" --become

[root@sindceaprhp25 dnsmasq.d]# rpm -qa | grep -i atomic
atomic-openshift-sdn-ovs-3.6.173.0.140-1.git.0.9686d52.el7.x86_64
atomic-openshift-clients-3.6.173.0.140-1.git.0.9686d52.el7.x86_64
atomic-registries-1.22.1-1.gitd36c015.el7.x86_64
atomic-openshift-docker-excluder-3.6.173.0.140-1.git.0.9686d52.el7.noarch
atomic-openshift-3.6.173.0.140-1.git.0.9686d52.el7.x86_64
atomic-openshift-excluder-3.6.173.0.140-1.git.0.9686d52.el7.noarch
atomic-openshift-node-3.6.173.0.140-1.git.0.9686d52.el7.x86_64
tuned-profiles-atomic-openshift-node-3.6.173.0.140-1.git.0.9686d52.el7.x86_64
atomic-openshift-utils-3.6.173.0.112-1.git.2.17a27d3.el7.noarch
[root@sindceaprhp25 dnsmasq.d]#


ansible-playbook -i hosts /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml --become

```
