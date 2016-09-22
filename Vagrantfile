# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
# Bryan Bui-Tuong

# Adjustable settings
CFG_TZ = "US/Pacific"     # timezone, like US/Pacific, US/Eastern, UTC, Europe/Warsaw, etc.

# Configure VM server
VAGRANTFILE_API_VERSION = "2"
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  
  # All Vagrant configuration is done here
  config.vm.define :delta do |x|
    # Every Vagrant virtual environment requires a box to build off of.
    x.vm.box = "ubuntu/trusty64"

    x.vm.provider :virtualbox do |v, override|
      v.name = "messenger-vbox"
      override.vm.network "private_network", ip: "10.3.3.3"
      # Forward ports for MongoDB and Express app
      override.vm.network "forwarded_port", guest: 27017, host: 27017, auto_correct: true
      override.vm.network "forwarded_port", guest: 3000, host: 3000, auto_correct: true
      override.vm.network "forwarded_port", guest: 8000, host: 8000, auto_correct: true
    end

    x.vm.provider :aws do |aws, override|
      aws.access_key_id = ENV['AWS_ACCESS_KEY']
      aws.secret_access_key = ENV['AWS_SECRET_KEY']
      aws.keypair_name = ENV['AWS_KEY_NAME']
      aws.ami = "ami-f898f698"
      aws.region = "us-west-1"
      aws.instance_type = "t2.micro"

      override.vm.box = "dummy"
      override.ssh.username = "ubuntu"
      override.ssh.private_key_path = ENV['AWS_KEY_PATH']
    end


    x.ssh.forward_agent = true

    # This will install the latest chef version.  This works on EC2 also.
    # Must run librarian-chef on dev computer to pull all the cookbooks first though.
    x.omnibus.chef_version = :latest

    x.vm.provision :shell, :inline => "sudo apt-get update -y"
    x.vm.provision :shell, :inline => "sudo apt-get install curl -y"
    #because we use omnibus, we can remove this.
    #x.vm.provision :shell, :inline => "curl -L https://www.opscode.com/chef/install.sh | sudo bash"

    x.vm.provision :chef_solo do |chef|
      # Paths to your cookbooks (on the host)
      chef.cookbooks_path = ["cookbooks"]
      # Add chef recipes
      chef.add_recipe 'nodejs'
      chef.add_recipe 'mongodb::default'
      chef.add_recipe 'git' # Is required for NPM
    end

    # Install express and express-generator packages globally
    x.vm.provision :shell, :inline => "npm -g install npm@latest-2"
    x.vm.provision :shell, :inline => "npm install -g express express-generator"
    x.vm.provision :shell, :inline => "npm install -g nodemon"

  end
end