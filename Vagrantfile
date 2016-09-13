# -*- mode: ruby -*-
# vi: set ft=ruby :

$master_num_instances = 1
$worker_num_instances = 3


def masterIP(num)
  return "10.9.8.#{num+6}"
end

def workerIP(num)
  return "10.9.8.#{num+50}"
end

MASTER_IPs = [*1..$master_num_instances].map{ |i| masterIP(i) }
WORKER_IPs = [*1..$worker_num_instances].map{ |i| workerIP(i) }

def configure_vm(vm, **opts)

    vm.provision "shell", inline: "apt-get install python -y"

    private_ip = "#{opts[:ip]}"

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
      ansible.extra_vars = {
        master_ip: MASTER_IPs[0]
      }
      ansible.groups = {
        "masters" => ["master-10.9.8.[5:10]"],
        "workers" => ["worker-10.9.8.[50:100]"]
      }

    end

end


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

    ip = masterIP(i)
    name = "master-#{ip}"

    config.vm.define name do |master|
      configure_vm(master.vm, ip: ip, name: name )
    end

  end

  (1..$worker_num_instances).each do |i|

    ip = workerIP(i)
    name = "worker-#{ip}"

    config.vm.define name do |worker|
      configure_vm(worker.vm, ip: ip, name: name )
    end

  end

end
