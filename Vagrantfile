# -*- mode: ruby -*-
# vi: set ft=ruby :

ENV['ANSIBLE_ROLES_PATH'] = "../../roles"

Vagrant.configure(2) do |config|

  config.vm.define "lxc_12" do |lxc_12_cfg|
    lxc_12_cfg.vm.box='ubuntu/precise64'
    lxc_12_cfg.vm.provider :virtualbox do |v|
      v.name = "lxc_12"
      # v.gui = true
    end
  end
  
  config.vm.define "lxc_14" do |lxc_14_cfg|
    lxc_14_cfg.vm.box='ubuntu/trusty64'
    lxc_14_cfg.vm.provider :virtualbox do |v|
      v.name = "lxc_14"
    end
  end
  
  config.vm.provision :ansible do |ansible|
    ansible.playbook = "examples/lxc.yml"
    # ansible.verbose = "vvv"
    ansible.sudo = true
    # ansible.tags = ['debug']
    ansible.groups = {
      "lxcs" => ["lxc_12", "lxc_14"],
    }
    ansible.extra_vars = {
      update_kernel_if_required: true,
      wait_for_reboot: 15,
      lxc_vms:
      [
        {
          name: "test1",
          type: "ubuntu",
          revision: "precise",
          servername: "test1.example.com",
          http_port: 5000,
          https: true
        },
        {
          name: "test2",
          type: "ubuntu",
          revision: "trusty",
          servername: "test2.example.com",
          http_port: 5000,
          https: false
        },
      ]
    }
  end
end
