# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vagrant.plugins = ["vagrant-reload", "vagrant-vbguest"]

  config.vm.box = "centos/7"

  config.vm.synced_folder ".", "/vagrant"

  # do not auto upgrade on boot
  config.vm.box_check_update = false
  config.vbguest.auto_update = false
  config.vbguest.no_remote = true

  config.vm.provider "virtualbox" do |vb|
    vb.gui = false
    vb.cpus = "1"
    vb.memory = "1024"
    vb.customize ["modifyvm", :id, "--audio", "none"]
  end

  config.vm.define "webhost0", autostart: true do |inst|
    inst.vm.hostname = "webhost0"

    inst.vm.network "private_network", ip: "192.168.10.10"

    inst.vm.network :forwarded_port, guest: 8080, host: 8181

    inst.vm.provision "ansible_local" do |ansible|
      ansible.compatibility_mode = "2.0"
      ansible.playbook = "provisioning/setup_vagrant.yaml"
    end

    # required to open outbound connections from mod_proxy due to selinux
    inst.vm.provision "shell", inline: "ansible-galaxy collection install ansible.posix --collections-path=/home/vagrant/.ansible/collections --force"

    inst.vm.provision "ansible_local" do |ansible|
      ansible.compatibility_mode = "2.0"

      ansible.galaxy_role_file = "provisioning/web_requirements.yaml"
      ansible.galaxy_roles_path = "/home/vagrant/.ansible/roles"
      ansible.galaxy_command = "ansible-galaxy install --role-file=%{role_file} --roles-path=%{roles_path}"

      ansible.playbook = "provisioning/web_vagrant.yaml"
    end
  end

  config.vm.define "wlshost0", autostart: true do |inst|
    inst.vm.hostname = "wlshost0"

    inst.vm.network "private_network", ip: "192.168.10.20"

    inst.vm.network :forwarded_port, guest: 7001, host: 17001

    inst.vm.provision "ansible_local" do |ansible|
      ansible.compatibility_mode = "2.0"
      ansible.playbook = "provisioning/setup_vagrant.yaml"
    end

    inst.vm.provision "ansible_local" do |ansible|
      ansible.compatibility_mode = "2.0"

      ansible.galaxy_role_file = "provisioning/wls_requirements.yaml"
      ansible.galaxy_roles_path = "/home/vagrant/.ansible/roles"
      ansible.galaxy_command = "ansible-galaxy install --role-file=%{role_file} --roles-path=%{roles_path}"

      ansible.playbook = "provisioning/wls_vagrant.yaml"
    end
  end

  config.vm.define "wlshost1", autostart: true do |inst|
    inst.vm.hostname = "wlshost1"

    inst.vm.network "private_network", ip: "192.168.10.21"

    inst.vm.provision "ansible_local" do |ansible|
      ansible.compatibility_mode = "2.0"
      ansible.playbook = "provisioning/setup_vagrant.yaml"
    end

    inst.vm.provision "ansible_local" do |ansible|
      ansible.compatibility_mode = "2.0"

      ansible.galaxy_role_file = "provisioning/wls_requirements.yaml"
      ansible.galaxy_roles_path = "/home/vagrant/.ansible/roles"
      ansible.galaxy_command = "ansible-galaxy install --role-file=%{role_file} --roles-path=%{roles_path}"

      ansible.playbook = "provisioning/wls_vagrant.yaml"
    end
  end

end
