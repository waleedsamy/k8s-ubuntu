# ubuntu-vagrant
Ubuntu vms with kubernetes installed. provisioned by ansible!

# How to use `Vagrant version`
 ```bash
 #cache apt-get, apt-list and pip. this plugin will save a lot of time while provisioning your machines
 $ vagrant plugin install vagrant-cachier

 $ sudo ansible-galaxy install geerlingguy.git angstwad.docker_ubuntu

 $ vagrant up
 # watch vagrant-cachier and feel like WTF
 # while :; do clear; tree ~/.vagrant.d/cache/ubuntu/trusty64/apt/; sleep 2; done
 ```

# How to use `VMs already created`
 If you have your VMs already created, the only thing you need to do is execute `ansible/playbook.yml` against your machines (VMs).
 * prepare your inventory file like
  ```
  master-10.9.8.7 ansible_ssh_host={host} ansible_ssh_port={port} ansible_ssh_user='{user}' ansible_ssh_private_key_file='{private_key}'
  worker-10.9.8.51 ansible_ssh_host={host} ansible_ssh_port={port} ansible_ssh_user='{user}' ansible_ssh_private_key_file='{private_key}'

  [masters]
  master-10.9.8.[5:10]

  [workers]
  worker-10.9.8.[50:100]
  ```
 * run ansible against your machines

  ```bash
  # extra-vars should has your master_ip
  $ ansible-playbook --connection=ssh --timeout=30 --inventory-file={inventory-file} --extra-vars='{"master_ip":"{master_ip}"}' -v ansible/playbook.yml
  ```

# Kubernetes
kubernetes is deployed with ansible, you will have 4 machines master-10.9.8.7, worker-10.9.8.51-53. it will be add to cluster automatically, if not need to add this nodes to the cluster by
```bash
$ vagrant ssh master-10.9.8.7
$ kubectl get nodes
# if it not created automatically use kubectl to create each node
$ sudo cat <<EOF | kubectl create -f -
{
  "kind": "Node",
  "apiVersion": "v1",
  "metadata": {
    "name": "10.9.8.51",
    "labels": {
      "name": "node-51",
      "env": "development"
    }
  }
}
EOF
```


# Misc
command I used to use, and forget it everytime.
```bash
$ ssh -p 2200 -i ./.vagrant/machines/master-7/virtualbox/private_key vagrant@127.0.0.1
```
