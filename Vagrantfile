VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "precise64"
  config.vm.box_url = "http://files.vagrantup.com/precise64.box"
  config.vm.network "private_network", ip: "192.168.13.37"
  config.vm.hostname = "appdev"

  #Add any alias:
  config.hostsupdater.aliases = ["app.dev","phpmyadmin.app.dev"]

  config.vm.synced_folder ".", "/vagrant", :nfs => true

  config.vm.provision "ansible" do |ansible|
    ansible.limit = 'all'
    ansible.playbook = "ansible/development.yml"
    ansible.inventory_path = "ansible/hosts"
  end

  config.vm.post_up_message = "Provisioning is done, visit http://app.dev for your CakePHP application! Visit http://phpmyadmin.app.dev for phpMyAdmin (MySQl credentials are root:temppassword)."
end
