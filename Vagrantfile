# -*- mode: ruby -*-
# vi: set ft=ruby :

def configure_vm(vm, master, **opts)

    private_ip = "10.9.8.#{opts[:ip]}"
    vm.box = opts.fetch(:box, "ubuntu/trusty64")
    vm.network :private_network, ip: private_ip

    vm.hostname = opts[:name]

    vm.provider :libvirt do |vb|
      vb.memory = 2084
      vb.cpus = 2
    end

    vm.provider :virtualbox do |vb|
      vb.memory = 2084
      vb.cpus = 2
    end

    vm.provision "ansible" do |ansible|
      ansible.playbook = "ansible/playbook.yml"
      ansible.verbose = true
      ansible.groups = {
        "masters" => ["master[5:10]"],
        "workers" => ["worker[50:60]"]
      }

    end

    if master then
      vm.provision "shell", inline: "IP_ADDRESS=#{private_ip} ~/kube-deploy/docker-multinode/master.sh"
    else
      vm.provision "shell", inline: "IP_ADDRESS=#{private_ip} MASTER_IP=#{opts[:master]} ~/kube-deploy/docker-multinode/worker.sh"
    end

end

$master_num_instances = 1
$worker_num_instances = 3
$master_ip = "10.9.8.7"

Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/trusty64"

  # plugin conflict
  if Vagrant.has_plugin?("vagrant-vbguest") then
    config.vbguest.auto_update = false
  end

  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.scope = :box
    config.cache.enable :apt_lists
    # work with linux
    # config.cache.enable :pip
  end

  (1..$master_num_instances).each do |i|

    ip = i + 6
    name = "master-#{ip}"

    config.vm.define name do |master|
      configure_vm(master.vm, true, ip: ip, name: name )
    end

  end

  (1..$worker_num_instances).each do |i|

    ip = i + 50
    name = "worker-#{ip}"

    config.vm.define name do |worker|
      configure_vm(worker.vm, false, ip: ip, name: name, master: $master_ip  )
    end

  end

end
