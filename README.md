# lxc role
This role install lxc, nginx and could set up containers and even proxy http for them. 

```
git clone https://github.com/gitinsky/ansible-role-lxc.git roles/lxc
git clone https://github.com/gitinsky/ansible-role-nginx.git roles/nginx
```

or:

```
git submodule add https://github.com/gitinsky/ansible-role-lxc.git roles/lxc
git submodule add https://github.com/gitinsky/ansible-role-nginx.git roles/nginx
```

You can disable nginx for the whole role with ```lxc_force_skip_nginx_for_all_vms: true```, nginx role will not be included in dependencies in this case. You can also disable nginx configs for individual containers, they will be deleted if already installed. See variables section.

Optional:
clone [letsencrypt](https://github.com/gitinsky/ansible-role-letsencrypt) role with ```git clone https://github.com/gitinsky/ansible-role-letsencrypt.git roles/letsencrypt``` or ```git submodule add https://github.com/gitinsky/ansible-role-letsencrypt.git roles/letsencrypt```
You might find [certificates](https://github.com/gitinsky/ansible-role-certificates) role useful as well, clone it with ```git clone https://github.com/gitinsky/ansible-role-certificates.git roles/certificates```


# Examples
#### playbook with self-signed certificates
```yml
- hosts: lxcs
  become: yes
  roles:
    - { role: certificates, ssl_cert_root: /etc/ssl, ssl_name: test1.example.com } 
    - { role: certificates, ssl_cert_root: /etc/ssl, ssl_name: test2.example.com } 
    - role: lxc
```

#### playbook with letsencrypt certificates
```yml
- hosts: lxcs
  become: yes
  roles:
    - { role: lxc, tags: lxc }
    - { role: letsencrypt, tags: cert } 
```

##### variables

You can define your containers in ```lxc_vms```, they will be automatically created with the user used to ssh to the host and with your ```~/.ssh/id_rsa.pub``` key. There's also a dirty task to install python with apt-get to get containers ready for ansible. Nginx will be configured to point hostnames to there containers, SNI is supported so you can have multiple https names.

```yml
lxc_vms:
  - { name: "test1", type: "ubuntu", revision: "trusty",  servername: "test1.example.com", http_port: 5000, https: on }
  - { name: "test2", type: "ubuntu", revision: "precise", servername: "test2.example.com", http_port: 80,   https: off, client_max_body_size: "50m" }
  - { name: "test3", type: "ubuntu", revision: "precise", no_nginx: true}
  - { name: "test4", type: "ubuntu", revision: "trusty",  servername: "test4.example.com", http_port: 5000, https: on, letsencrypt: yes }
  - { name: "test5", type: "ubuntu", revision: "trusty",  servername: "test5.example.com", http_port: 5000, https: on, letsencrypt: yes, letsencrypt_name: "test4.example.com" }

letsencrypt:
    - { email: "hostmaster@example.com", domains: ["test4.example.com", "test5.example.com"] }

```

Note that here we've defined test4 and test5 containers to use the single letsencrypt certificate. I could be a good practice as letsencrypt API calls are limited.

There’s also an ```update_kernel_if_required``` variable. If you set it to ```true```, your 12.04 ubuntu might get a kernel update. Your system will be rebooted in this case!

```yml
lxc_domain: lxc
```

If ```lxc_domain``` is set ```LXC_DOMAIN``` option in a ```/etc/default/lxc-net``` will be set to it's value. 

# ssh configuration

There’s a ```generate_sshconf``` variable enabled by default. It it’s on, role will generate ssh proxy command configuration for the lxc vms at your ```/.ssh/config.d``` directory. You’ll have to merge it to ```/.ssh/config``` yourself. Here’s the script I use:

```bash
#!/bin/bash
ls ~/.ssh/config.d/ | {
    echo -n '' > ~/.ssh/config
    while read file
    do
        cat ~/.ssh/config.d/$file >> ~/.ssh/config
        echo '' >> ~/.ssh/config
    done
}
```

There’s also an ```sshconf``` tag that could be used right for ssh configuration generation

# Notes

First container creation takes time. It took vagrant 53 minutes to start two different containers.

Tested on ubuntu 14.04 and 12.04 with ubuntu 12.04 and 14.04 containers, ansible 1.8.4

Fo user installation kernel should be at least 3.5.. To update it on 12.04, run

```
sudo apt-get install linux-image-generic-lts-raring linux-headers-generic-lts-raring
```
