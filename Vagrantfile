# -*- mode: ruby -*-
# vi: set ft=ruby :
# Required plugins:
#
# vagrant-env (0.0.3)
# vagrant-faster (0.2.0)
# vagrant-hostmanager (1.8.8)
# vagrant-hosts-provisioner (2.0)
# vagrant-share (1.1.9)
# vagrant-timezone (1.2.0)
# vagrant-vbguest (0.15.1)


# Parameters
# All Vagrant configuration is done in the .env file below.
# For a complete reference, please see the online documentation at
# https://docs.vagrantup.com.
# Do something different between *nix and Windows ?
if Vagrant::Util::Platform.windows? then
    puts "Running on Windows"
else
    puts "Running on Linux or Mac"
end
# A fancy nameo?
if ! ENV['VVV_SKIP_LOGO'] then
	puts ""
	puts "  \033[38;5;206mA fancy name \033[38;5;118m1.0.0"
	puts ""
	puts "\033[0m"
end
Vagrant.configure("2") do | config |
  config.env.enable # Reads configuration values from .env
  # Set to true to autopudate vbguest (could lead to errors if you dont have the very last version of virtualbox)
  config.vbguest.auto_update = ENV['vbguestupdate']
  config.vm.box = ENV['box']
  config.vm.box_check_update = true
  config.vm.define  ENV['name']
config.vm.hostname = ENV['name']
 # Network
  config.vm.network "private_network", type: "dhcp"
 # Provider 
  config.vm.provider "virtualbox" do |vb|
		# Display the VirtualBox GUI when booting the machine
		vb.gui = false
    # Commented out because memory and cpus are managed by vagrant-faster
		#vb.memory = "2048" 
		#vb.cpus = 2
		vb.name = ENV['name']
	  vb.linked_clone = true if Gem::Version.new(Vagrant::VERSION) >= Gem::Version.new('1.8.0')
	end
  # Explicitely shares the provision folder 
  config.vm.synced_folder "provision", "/host"
  # Keeps in sync git and ssh configuration
  config.vm.provision "file", run: "always", source: "~/.gitconfig", destination: ".gitconfig"
  config.vm.provision "file", run: "always", source: "~/.ssh/", destination: "host_ssh"
  # Standard provion organization suggestion
  # Repos installations (once as root)
	#config.vm.provision "s00", type:"shell", name: "mysql", path: "provision/00repos.sh"
  # Lamp config (once as root)
	#config.vm.provision "s10", type:"shell", name: "lamp",  path: "provision/10lamp.sh"
  # User conf (both root and vagrant always as vagrant)
	config.vm.provision "u20", type:"shell", name: "user",  path: "provision/20user.sh", privileged: false, run: "always"
  # Specific configuration (once as root)
  config.vm.provision "s30", type:"shell", name: "app",   path: "provision/30app.sh"
  # Composer installation (once as vagrant)
  config.vm.provision "u40", type:"shell", name: "comp",  path: "provision/40composer.sh", privileged: false
	#if Vagrant::Util::Platform.windows? then
	#	config.vm.synced_folder "provision", "/host", # user: "root" , group: "root"#, type: "sshfs"
	#	config.vm.synced_folder ".", "/vagrant", type: "virtualbox" ,disabled: true #
	#else
	#	config.vm.synced_folder "provision", "/host" #, user: "root" , group: "root"
	#end
	config.ssh.forward_agent = true
	config.ssh.forward_x11 = true
  #Displays current ip (assumes a linux vagrant box)
  config.vm.provision "ip",    type:"shell", run: "always", name: "ip",    inline:"sudo ifconfig | grep 'inet addr'"
  #Host file management
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.manage_guest = false
  config.hostmanager.ignore_private_ip = false
  config.hostmanager.include_offline = false
  config.hostmanager.aliases = %w(erp.amtitalia.com www.uno8uno.it erp.webgrind.test)
  config.hostmanager.ip_resolver = proc do |vm, resolving_vm|
    if hostname = (vm.ssh_info && vm.ssh_info[:host])
      `vagrant ssh -c "/sbin/ifconfig enp0s8 " | grep "inet addr" | tail -n 1 | egrep -o "[0-9\.]*" | head -n 1 2>&1`.split("\n").first[/(\d+\.\d+\.\d+\.\d+)/, 1]
    end
  end
