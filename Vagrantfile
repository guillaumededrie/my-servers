# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "archlinux/archlinux"

  config.vm.network "private_network", ip: "192.168.12.10"
  config.vm.synced_folder ".", "/vagrant", disabled: true

  config.vm.provider "virtualbox" do |vb|
    vb.memory = 2048
    vb.cpus = 2
  end

  config.vm.provider "libvirt" do |libvirt|
    libvirt.memory = 2048
    libvirt.cpus = 2
    libvirt.cpu_mode = "host-passthrough"
    libvirt.volume_cache = "writeback"
  end

  config.vm.provision "ansible" do |ansible|
    ansible.inventory_path = "inventory.dev.ini"

    ansible.galaxy_role_file = "roles/requirements.yml"
    ansible.playbook = "system_administration.yml"
    ansible.raw_arguments = ['--become-method=sudo']
  end
end
