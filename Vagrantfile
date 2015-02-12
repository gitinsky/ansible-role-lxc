# -*- mode: ruby -*-
# vi: set ft=ruby :

# defaultbox = "precise64"
defaultbox = "ubuntu/trusty64"
box = ENV['BOX'] || defaultbox
ENV['ANSIBLE_ROLES_PATH'] = "../../roles"

Vagrant.configure(2) do |config|

  config.vm.box = box
  if box == defaultbox
  end
  
  config.vm.define "lxc" do |lxc_cfg|
    # lxc_cfg.vm.network "private_network", type: "dhcp"
    # lxc_cfg.vm.network :forwarded_port, host: 5000, guest: 5000
    lxc_cfg.vm.provider :virtualbox do |v|
      v.name = "lxc"
      v.memory = 512
      # v.gui = true
    end
  end
  
  config.vm.provision :ansible do |ansible|
    ansible.playbook = "examples/lxc.yml"
    # ansible.verbose = "vvv"
    ansible.sudo = true
    # ansible.tags = ['config']
    ansible.groups = {
      "lxcs" => ["lxc"],
    }
    ansible.extra_vars = {
      ansible_ssh_user: 'vagrant',
      hbase_standalone: true,
      lxc_vms:
      [
        # {
        #   name: "test01",
        #   type: "ubuntu",
        #   revision: "precise",
        # },
        {
          name: "demo",
          type: "ubuntu",
          revision: "precise",
          servername: "test1.example.com",
          http_port: 5000
        },
      ]
    }
    
  end

end
