VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "ubuntu/xenial64"
  config.vm.network "private_network", ip: "192.168.13.37"
  config.vm.hostname = "appdev"

  #Add any alias:
  config.hostsupdater.aliases = [
    "app.dev",
    "phpmyadmin.app.dev"
  ]

  config.vm.synced_folder ".", "/vagrant", :nfs => true

  #Fix for Ansible bug resulting in an encoding error
  ENV['PYTHONIOENCODING'] = "utf-8"

  config.vm.provision "ansible" do |ansible|
    ansible.limit = 'all'
    ansible.playbook = "ansible/development.yml"
    ansible.inventory_path = "ansible/hosts"
  end

  config.vm.post_up_message = "\n\nProvisioning is done, visit http://app.dev for your CakePHP application! \n\nVisit http://phpmyadmin.app.dev for phpMyAdmin (MySQl credentials are root:temppassword).\n\n"
end
