# ubuntu-vagrant
Ubuntu vms with kubernetes installed. provisioned by ansible!

# How to use
 ```bash
 #cache apt-get, apt-list and pip. this plugin will save a lot of time while provisioning your machines
 vagrant plugin install vagrant-cachier

 sudo ansible-galaxy install geerlingguy.git angstwad.docker_ubuntu

 vagrant up
 # watch vagrant-cachier and feel like WTF
 # while :; do clear; tree ~/.vagrant.d/cache/ubuntu/trusty64/apt/; sleep 2; done
 ```

# Kubernetes
kubernetes is deployed with ansible, you will have 3 machines master-7, worker-51 and worker-52.  you need to add this nodes to the cluster manually by
```bash
$ vagrant ssh master-7
$ sudo su -
# use kubectl to create each node
$ cat <<EOF | kubectl create -f -
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
ssh -p 2200 -i ./.vagrant/machines/master-7/virtualbox/private_key vagrant@127.0.0.1
```
