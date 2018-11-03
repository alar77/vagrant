# -*- mode: ruby -*-
# vi: set ft=ruby :
# Required plugins:
#
# vagrant-env (0.0.3)
# vagrant-faster (0.2.0)
# vagrant-hostmanager (1.8.8)
# vagrant-share (1.1.9)
# vagrant-timezone (1.2.0)
# vagrant-vbguest (0.15.1)
ROOT = File.dirname(__FILE__)
unless File.file?(ROOT + "/.env")
  puts "Please create a " + ROOT + "/.env" +" file. Check dot_env-example"
  exit
end
plugins= [
  'vagrant-env',
  'vagrant-faster',
  'vagrant-share',
  'vagrant-timezone',
  'vagrant-vbguest',
  'copy_my_conf',
  'vagrant-address',
  'vagrant-hostsupdater'

]

needrebot=false
for plugin in plugins do
  unless Vagrant.has_plugin?(plugin)
    system("vagrant plugin install " + plugin)
    needreboot=true
  end
end
if needreboot
  puts "Dependencies installed, please try the command again."
  exit
end
#  Command requested is in ARGV[0]
# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  config.env.enable # Reads configuration values from .env
  required=['name','box']
  for key in required do
    unless ENV[key]
      puts "Please, fill '" + key +"'"
      exit
    end
    if ENV[key] == "FILLME"
      puts "Please, fill '" + key +"'"
      exit
    end
  end
  if ! ENV['VVV_SKIP_LOGO'] then
    puts "  \033[38;5;206m" +ENV['name'] + " \033[38;5;118m 1.0.0"
    puts "\033[0m"
  end
  # Set to true to autopudate vbguest (could lead to errors if you dont have the very last version of virtualbox)
  config.vbguest.auto_update = ENV['vbguestupdate']
  config.vm.box = ENV['box']
  config.vm.box_check_update = true
  config.vm.define  ENV['name']+'.test'
  config.vm.hostname = ENV['name']+'.test'
  config.hostsupdater.aliases = ["www." + ENV['name'] + ".test","webgrind." + ENV['name'] + ".test"]
  config.vm.box = ENV['box']
  config.vm.network "private_network", ip: "192.168.33.11"
  config.vm.provision :copy_my_conf do |copy_conf|
    copy_conf.git
    copy_conf.ssh
  end
  config.vm.provision "ansible_local" do |ansible|
    ansible.compatibility_mode = "auto"
    ansible.extra_vars = {
      'hostname' => ENV['name']
    }
    ansible.playbook = "playbook.yml"
  end
end
