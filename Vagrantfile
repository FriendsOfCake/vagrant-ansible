#######################################
# You can edit the settings bellow
#######################################
ENV['VAGRANT_HOSTNAME'] = "cake.dev" #Set the name of your server
ENV['VAGRANT_IP'] = "192.168.13.16" #Set the ip you wish to use


# ! DO NOT EDIT BELOW
VAGRANTFILE_API_VERSION = "2"

#Check Vagrant hostsupdater plugin is installed, if not provice command to do so
unless Vagrant.has_plugin?("vagrant-hostsupdater")
  raise "\n\nvagrant-hostsupdater plugin is not installed! Please install by running:\n\n $ vagrant plugin install vagrant-hostsupdater\n"
end

#Configure the box
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "trusty64"
  config.vm.box_url = "http://cloud-images.ubuntu.com/vagrant/trusty/current/trusty-server-cloudimg-amd64-vagrant-disk1.box"
  config.vm.network "private_network", ip: ENV['VAGRANT_IP']
  config.vm.hostname = ENV['VAGRANT_HOSTNAME'].gsub(/[^0-9a-z ]/i, '')

  #Add any alias:
  config.hostsupdater.aliases = [
    ENV['VAGRANT_HOSTNAME'],
    "phpmyadmin."+ENV['VAGRANT_HOSTNAME'],
    "phppgadmin."+ENV['VAGRANT_HOSTNAME']
  ]

  #Link the /vagrant dir on the host to this local dir using nfs
  config.vm.synced_folder ".", "/vagrant", :nfs => true

  #Write the hostname / ip to the Ansible inventory file:
  File.open('ansible/hosts', 'w') { |file| file.write("[development]\n"+ENV['VAGRANT_IP']) }

  #Fix for Ansible bug resulting in an encoding error
  ENV['PYTHONIOENCODING'] = "utf-8"

  #Use ansible to privision the server
  config.vm.provision "ansible" do |ansible|
    ansible.limit = 'all'
    ansible.playbook = "ansible/development.yml"
    ansible.inventory_path = "ansible/hosts"
  end

  config.vm.post_up_message = "\n - Visit http://"+ENV['VAGRANT_HOSTNAME']+" for your CakePHP application.\n - Visit http://phpmyadmin."+ENV['VAGRANT_HOSTNAME']+" for phpMyAdmin (if chosen)\n - Visit http://phppgadmin."+ENV['VAGRANT_HOSTNAME']+" for phpPgAdmin (if chosen)\n\n"
end
