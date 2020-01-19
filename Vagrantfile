# -*- mode: ruby -*-
# vi: set ft=ruby :

$AMQ1_IP = "172.16.157.2"
$AMQ2_IP = "172.16.157.3"
$AMQ3_IP = "172.16.157.4"
$AMQ1_NUM_CPUS = 4
$AMQ2_NUM_CPUS = 4
$AMQ3_NUM_CPUS = 4
$AMQ1_MEM_MBS = 4096
$AMQ2_MEM_MBS = 4096
$AMQ3_MEM_MBS = 4096

Vagrant.configure("2") do |config|
  config.vm.define "amq1" do |amq1|
    amq1.vm.box = "generic/ubuntu1804"
    amq1.vm.hostname = "amq1"
    amq1.vm.network "private_network", ip: $AMQ1_IP

    amq1.vm.provider :virtualbox do |v, override|
      v.gui = false
      v.customize ["modifyvm", :id, "--cpus", $AMQ1_NUM_CPUS]
      v.customize ["modifyvm", :id, "--memory", $AMQ1_MEM_MBS]
      v.customize ["modifyvm", :id, "--cableconnected1", "on"]
      v.customize ["modifyvm", :id, "--cableconnected2", "on"]
    end

    amq1.vm.provider :libvirt do |v, override|
      v.cpu_mode = 'custom'
      v.cpu_model = 'kvm64'
      v.volume_cache = 'writeback'
      v.disk_bus = 'virtio'
      v.cpus = $AMQ1_NUM_CPUS
      v.memory = $AMQ1_MEM_MBS
    end

    amq1.vm.provision "shell", inline: "apt-get install -y python"
    amq1.vm.provision "shell", inline: "sysctl net.ipv6.conf.all.disable_ipv6=1"
    amq1.vm.provision "shell", inline: "sysctl net.ipv6.conf.default.disable_ipv6=1"
  end

  config.vm.define "amq2" do |amq2|
    amq2.vm.box = "generic/ubuntu1804"
    amq2.vm.hostname = "amq2"
    amq2.vm.network "private_network", ip: $AMQ2_IP

    amq2.vm.provider :virtualbox do |v, override|
      v.gui = false
      v.customize ["modifyvm", :id, "--cpus", $AMQ2_NUM_CPUS]
      v.customize ["modifyvm", :id, "--memory", $AMQ2_MEM_MBS]
      v.customize ["modifyvm", :id, "--cableconnected1", "on"]
      v.customize ["modifyvm", :id, "--cableconnected2", "on"]
    end

    amq2.vm.provider :libvirt do |v, override|
      v.cpu_mode = 'custom'
      v.cpu_model = 'kvm64'
      v.volume_cache = 'writeback'
      v.disk_bus = 'virtio'
      v.cpus = $AMQ2_NUM_CPUS
      v.memory = $AMQ2_MEM_MBS
    end

    amq2.vm.provision "shell", inline: "apt-get install -y python"
    amq2.vm.provision "shell", inline: "sysctl net.ipv6.conf.all.disable_ipv6=1"
    amq2.vm.provision "shell", inline: "sysctl net.ipv6.conf.default.disable_ipv6=1"
  end

  config.vm.define "amq3" do |amq3|
    amq3.vm.box = "generic/ubuntu1804"
    amq3.vm.hostname = "amq3"
    amq3.vm.network "private_network", ip: $AMQ3_IP

    amq3.vm.provider :virtualbox do |v, override|
      override.vm.synced_folder ".", "/vagrant"
      v.gui = false
      v.customize ["modifyvm", :id, "--cpus", $AMQ3_NUM_CPUS]
      v.customize ["modifyvm", :id, "--memory", $AMQ3_MEM_MBS]
      v.customize ["modifyvm", :id, "--cableconnected1", "on"]
      v.customize ["modifyvm", :id, "--cableconnected2", "on"]
    end

    amq3.vm.provider :libvirt do |v, override|
      override.vm.synced_folder ".", "/vagrant", type: "nfs"
      v.cpu_mode = 'custom'
      v.cpu_model = 'kvm64'
      v.volume_cache = 'writeback'
      v.disk_bus = 'virtio'
      v.cpus = $AMQ3_NUM_CPUS
      v.memory = $AMQ3_MEM_MBS
    end

    amq3.vm.provision "shell", inline: "apt-get install -y python"
    amq3.vm.provision "shell", inline: "sysctl net.ipv6.conf.all.disable_ipv6=1"
    amq3.vm.provision "shell", inline: "sysctl net.ipv6.conf.default.disable_ipv6=1"
    amq3.vm.provision "ansible_local" do |ansible|
      ansible.playbook = "ansible/site.yml"
      ansible.config_file = "ansible/ansible.cfg"
      ansible.inventory_path = "ansible/inventory"
      ansible.become = true
      ansible.galaxy_role_file = "ansible/roles/requirements.yml"
      ansible.galaxy_roles_path = "/etc/ansible/roles"
      ansible.galaxy_command = "sudo ansible-galaxy install --role-file=%{role_file} --roles-path=%{roles_path} --force"
      ansible.limit = "all"
    end
  end
end
