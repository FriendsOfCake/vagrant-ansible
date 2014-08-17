# Vagrant Ansible for CakePHP

Vagrant Ansible creates a Vagrant installation for CakePHP using Ansible with the following features:

- Ubuntu 14.04 Trusty Tahr (Ubuntu Server 64-bit 14.04 cloud image)
- Ningx 1.4.6
- PHP 5.5
- Percona 5.5 (MySQL Drop-in Replacement)
- Postgres 9.3
- Redis 2.8
- Memcached 1.4
- Git 1.9
- Composer
- phpMyAdmin 4.0.10


## Requirements

- [VirtualBox](https://www.virtualbox.org/wiki/Downloads). Tested on 4.3.x, but 4.2.x should also work.
- [Vagrant](http://www.vagrantup.com/downloads.html). Tested on 1.6.3
- [Vagrant hostupdater](https://github.com/cogitatio/vagrant-hostsupdater).
- [Ansible](http://docs.ansible.com/intro_installation.html). Tested on 1.7 Requires at least 1.6!

## Installation

1. Run the commands below:

	```
	git clone https://github.com/FriendsOfCake/vagrant-ansible.git --recurse-submodules
	cd vagrant-ansible
	vagrant up
	```

2. Choose the CakePHP version you want to deploy (2 or 3)

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

Add a new `server` block for each application, change `$appName` with your application name:

```
- server:
	file_name: $appName
	listen: 80
	server_name: $appName.dev
	root: /path/to/app/webroot
	access_log: /var/log/nginx/$appName-access.log
	error_log: /var/log/nginx/$appName-error.log
	index: index.php
	location1:
		name: /
		try_files: "$uri $uri/ /index.php?$uri&$args"
		expires: max
		access_log: "off"
	location2:
		name: "~ \\.php$"
		fastcgi_pass: "unix:/var/run/php5-fpm.sock"
		include: fastcgi_params

# Redirect www.$appName.dev to $appName.dev
- server:
	file_name: www.$appName
	listen: 80
	server_name: www.$appName.dev
	rewrite: "^ http://$appName.dev$uri permanent"
```


## Credits

This project was totally inspired from [FriendsOfCake/vagrant-chef](https://github.com/FriendsOfCake/vagrant-chef).

Many thanks to [josegonzalez](https://github.com/josegonzalez)!


## MIT

Copyright (c) 2014 Renan Gonçalves

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE
