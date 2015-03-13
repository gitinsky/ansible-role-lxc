# lxc role
This role install lxc, nginx and could set up containers and even proxy http for them. 

```
git clone https://github.com/gitinsky/ansible-role-lxc.git roles/lxc
git clone https://github.com/gitinsky/ansible-role-nginx.git roles/nginx
```

Optional: clone [certificates](https://github.com/gitinsky/ansible-role-certificates) role with ```git clone https://github.com/gitinsky/ansible-role-certificates.git roles/certificates```

# Examples
#### playbook
```
- hosts: lxcs
  sudo: yes
  roles:
    - { role: certificates, ssl_cert_root: /etc/ssl, ssl_name: test1.example.com } 
    - { role: certificates, ssl_cert_root: /etc/ssl, ssl_name: test2.example.com } 
    - role: lxc
```

##### variables

You can define your containers in ```lxc_vms```, they will be automatically created with the user used to ssh to the host and with your ```~/.ssh/id_rsa.pub``` key. There's also a dirty task to install python with apt-get to get containers ready for ansible. Nginx will be configured to point hostnames to there containers, SNI is supported so you can have multiple https names.

```
lxc_vms:
  - { name: "test1", type: "ubuntu", revision: "trusty",  servername: "test1.example.com", http_port: 5000, https: on }
  - { name: "test2", type: "ubuntu", revision: "precise", servername: "test2.example.com", http_port: 80,   https: off }

```

# Notes

First container creation takes time. It took vagrant 53 minutes to start two different containers.

Tested on ubuntu 14.04 and 12.04 with ubuntu 12.04 and 14.04 containers, ansible 1.8.4

Fo user installation kernel should be at least 3.5.. To update it on 12.04, run

```
sudo apt-get install linux-image-generic-lts-raring linux-headers-generic-lts-raring
```