# -*- mode: ruby -*-
# vi: set ft=ruby :

$script = <<-SCRIPT
apt-get update
apt-get install -y python-pip
SCRIPT

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|

  config.vm.boot_timeout = 900
  config.vm.graceful_halt_timeout=100

  # vagrant-vbguest
  # set auto_update to false, if you do NOT want to check the correct
  # additions version when booting this machine
  if Vagrant.has_plugin?("vagrant-vbguest")
    config.vbguest.auto_update = false
  end

  # Vagrant Host Manager
  if Vagrant.has_plugin?("vagrant-hostmanager")
    config.hostmanager.enabled = true
    config.hostmanager.manage_host = true
    config.hostmanager.manage_guest = true
    config.hostmanager.ignore_private_ip = false
    config.hostmanager.include_offline = true
  end

  # Configure the window for gatling to coalesce writes.
  if Vagrant.has_plugin?("vagrant-gatling-rsync")
    config.gatling.latency = 2.5
    config.gatling.time_format = "%H:%M:%S"
  end

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "bento/debian-9.9"
  config.vm.box_version = "201907.07.0"
  # Don't check for box update
  config.vm.box_check_update = false

  # OpenShift client box
  config.vm.define "openshift-client", primary: true do |h|
    h.vm.hostname = 'openshift-client.site'
    h.vm.network "private_network", ip: "192.168.56.100"

    # Customize VirtualBox Provider
    h.vm.provider "virtualbox" do |vb|
      vb.name = "openshift-client"
      vb.memory = 1024
    end

  end

  # Provision Box
  config.vm.provision "shell", inline: $script

  ## Provision Box 
  config.vm.provision "ansible_local" do |ansible|
    ansible.install_mode = "pip"
    ansible.version = "2.8.0"
    ansible.galaxy_role_file = "requirements.yml"
    ansible.galaxy_roles_path = "/vagrant/roles"
    ansible.playbook = "openshift-client"
    ansible.verbose = true
  end

  # Automatically sync when machines with rsync folders come up.
  config.gatling.rsync_on_startup = false
end
