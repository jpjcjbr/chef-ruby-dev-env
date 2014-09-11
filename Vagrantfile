# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # Use Ubuntu 14.04 Trusty Tahr 64-bit as our operating system
  config.vm.box = "ubuntu/trusty64"

  # Configurate the virtual machine to use 2GB of RAM
  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--memory", "2048"]
  end

  # Forward the Rails server default port to the host
  config.vm.network :forwarded_port, guest: 3000, host: 3000
  config.vm.network :forwarded_port, guest: 5432, host: 5432
  config.vm.network :forwarded_port, guest: 1025, host: 1025
  config.vm.network :forwarded_port, guest: 10800, host: 10800

  # JP CONFIG
  config.ssh.forward_agent = true
  config.vm.synced_folder "/Users/jpjcjbr/projects/dev", "/home/vagrant/projects"

  # Use Chef Solo to provision our virtual machine
  config.vm.provision :chef_solo do |chef|
    chef.cookbooks_path = ["cookbooks", "site-cookbooks"]

    chef.add_recipe "apt"
    chef.add_recipe "nodejs"
    chef.add_recipe "ruby_build"
    chef.add_recipe "rbenv::user"
    chef.add_recipe "rbenv::vagrant"
    chef.add_recipe "vim"
    chef.add_recipe "postgresql::server"

    # Generate md5 password
    # echo -n 'q1w2e3r4''postgres' | openssl md5 | sed -e 's/.* /md5/'
    chef.json = {
      rbenv: {
        user_installs: [{
          user: 'vagrant',
          rubies: ["2.1.2"],
          global: "2.1.2",
          gems: {
            "2.1.2" => [
              { name: "bundler" }
            ]
          }
        }]
      },
      postgresql: {
        config: {
          listen_addresses: "*"
        },
        pg_hba: [{:comment => '# Liberar acesso a todos usuarios de qualquer host',
          :type => 'host', :db => 'all', :user => 'all', :addr => "0.0.0.0/0", :method => 'md5'}],
        password: {
          postgres: "md5e0cd61f4f502d955d3871f56b7ed56f1"
        }
      }
    }
  end
end