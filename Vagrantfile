# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrant init uses double quotes so makes sense to ignore it in rubocop
# rubocop:disable StringLiterals
# rubocop:disable Metrics/LineLength

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "ubuntu/trusty64"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  config.vm.network "forwarded_port", guest: 5432, host: 1234

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  config.vm.network "private_network", ip: "10.0.0.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", name: "locale", inline: <<-SHELL
    sudo locale-gen en_GB.UTF-8
    sudo update-locale LANG=en_GB.UTF-8 LANGUAGE
  SHELL

  config.vm.provision "shell", name: "postgresql", inline: <<-SHELL
    # Create apt repo file for postgres
    sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'

    # Import the repository signing key, and update the package lists
    wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -

    # Update package lists
    sudo apt-get update
    # Upgrade packages
    sudo apt-get -y upgrade

    # Libraries and headers for C language frontend development
    sudo apt-get -y install libpq-dev

    # Install postgres
    sudo apt-get -y install postgresql-9.4

    # Additional supplied modules
    sudo apt-get -y install postgresql-contrib-9.4

    # Configure PostgreSQL - postgresql.conf
    sudo /bin/cp /vagrant/postgresql.conf /etc/postgresql/9.4/main/postgresql.conf
    sudo service postgresql restart

    # Configure PostgreSQL - pg_hba.conf
    sudo /bin/cp /vagrant/pg_hba.conf /etc/postgresql/9.4/main/pg_hba.conf
    sudo service postgresql restart

    # Create postgresql user
    sudo -u postgres bash -c "psql -f /vagrant/create_user.sql"

    # Make sure the postgis extensions are installed
    sudo apt-get -y install libgeos-c1
    sudo apt-get -y install 'postgresql-9.4-postgis-2.1'

    sudo apt-get -y autoremove
  SHELL
end
