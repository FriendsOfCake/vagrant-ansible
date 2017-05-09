# Vagrant Ansible for CakePHP

Vagrant Ansible creates a Vagrant installation for CakePHP using Ansible with the following features:

- Ubuntu 16.04 LTS
- Ningx
- PHP
- Percona (MySQL Drop-in Replacement)
- Postgres
- Redis
- Memcached
- Git
- Composer
- phpMyAdmin


## Requirements

- [VirtualBox](https://www.virtualbox.org/wiki/Downloads). Tested on 5.x
- [Vagrant](http://www.vagrantup.com/downloads.html). Tested on 1.9.x
- [Vagrant hostupdater](https://github.com/cogitatio/vagrant-hostsupdater).
- [Ansible](http://docs.ansible.com/intro_installation.html). Requires 2.x

## Installation

1. Run the commands below:

	```
	git clone https://github.com/FriendsOfCake/vagrant-ansible.git --recurse-submodules
	cd vagrant-ansible
	vagrant plugin install vagrant-hostsupdater
	vagrant up
	```

2. Choose the CakePHP version you want to deploy (2 or 3), defaults to 3.

3. Note that this Vagrant machine uses NFS mounts as it significantly improves the speed of the application. You may be asked for the root password while trying to setup shared mounts.

4. The setup will take some time to finish. Sit back and enjoy!

5. When the setup is done browse to `http://app.dev/` in your browser, and you should have a default CakePHP 2.x or 3.x welcome page!


## Installation for Multiple Applications

To run Vagrant Ansible for multiple applications a few configurations have to be done.

```
git clone git@github.com:FriendsOfCake/vagrant-ansible.git --recurse-submodules
cd vagrant-ansible
```

After configured, run it with:

```
vagrant up
```

### Custom Domain Name

Edit your Vagrantfile to have the following line:

```
config.hostsupdater.aliases = [
	"app.dev",
	"phpmyadmin.app.dev",
	"whatever.dev",
	"anotherapp.dev"
]
```

### Configure Synced Folders

Open `Vagrantfile` in your editor of choice.

Add a new [synced_folder](http://docs.vagrantup.com/v2/synced-folders/basic_usage.html) for each application, like so:

```
config.vm.synced_folder "/path/on/local", "/path/on/vagrant", :nfs => true
```

### Configure Nginx virtual hosts

Open `ansible/group_vars/development` in your editor of choise.

Add a new block for each application, name the block your new app name.

```
  mynewapp:
    - listen 80
    - server_name mynewapp.dev
    - root /vagrant/www/webroot
    - access_log /var/log/nginx/mynewapp-access.log
    - error_log /var/log/nginx/mynewapp-error.log
    - index index.php
    - location / {
        try_files $uri $uri/ /index.php?$args;
      }
    - location ~ \.php$ {
        try_files $uri =404;
        include /etc/nginx/fastcgi_params;
        fastcgi_pass unix:/var/run/php7.1-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
      }
```


## Credits

This project was totally inspired from [FriendsOfCake/vagrant-chef](https://github.com/FriendsOfCake/vagrant-chef).

Many thanks to [josegonzalez](https://github.com/josegonzalez) and [helloandre](https://github.com/helloandre)!


## MIT

Copyright (c) 2014 Renan Gonçalves

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE
