# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

VAR_DOMAIN = "femog008.internal"
VAR_ROPOSITORY = "D:/VM/Shared/" #shared folder path. Edit this path.

all_nodes = [
	{ :host => "ans-svr-1", :ip => "172.168.2.10", :box => "bento/ubuntu-20.04", :ram => 2048, :cpu => 2, :gui => false, :vram_default => true },
	{ :host => "ans-svr-2", :ip => "172.168.2.11", :box => "bento/ubuntu-20.04", :ram => 2048, :cpu => 2, :gui => false, :vram_default => true },
	{ :host => "ans-svr-3", :ip => "172.168.2.12", :box => "bento/ubuntu-20.04", :ram => 2048, :cpu => 2, :gui => false, :vram_default => true },
	{ :host => "dck-svr-1", :ip => "172.168.2.13", :box => "kbsmanikanta/centos8-gui", :ram => 2048, :cpu => 2, :gui => false, :vram_default => false },
	{ :host => "dck-svr-2", :ip => "172.168.2.14", :box => "bento/centos-8.3", :ram => 2048, :cpu => 2, :gui => false, :vram_default => true },
	{ :host => "dck-svr-3", :ip => "172.168.2.15", :box => "bento/centos-8.3", :ram => 2048, :cpu => 2, :gui => false, :vram_default => true },
	{ :host => "jnk-svr-1", :ip => "172.168.2.16", :box => "peru/ubuntu-20.04-desktop-amd64", :ram => 8192, :cpu => 2, :gui => false, :vram_default => false},
	{ :host => "db-svr-1", :ip => "172.168.2.17", :box => "bento/ubuntu-20.04", :ram => 1024, :cpu => 2, :gui => true, :vram_default => true },
	{ :host => "db-svr-2", :ip => "172.168.2.18", :box => "bento/ubuntu-20.04", :ram => 1024, :cpu => 2, :gui => false, :vram_default => true },
	{ :host => "k8s-svr-1", :ip => "172.168.2.19", :box => "bento/ubuntu-20.04", :ram => 2048, :cpu => 2, :gui => false, :vram_default => true },
	{ :host => "k8s-svr-2", :ip => "172.168.2.20", :box => "bento/ubuntu-20.04", :ram => 2048, :cpu => 2, :gui => false, :vram_default => true },
	{ :host => "k8s-svr-3", :ip => "172.168.2.21", :box => "bento/ubuntu-20.04", :ram => 2048, :cpu => 2, :gui => false, :vram_default => true },
	{ :host => "bb-cli-1", :ip => "172.168.2.22", :box => "bento/ubuntu-20.04", :ram => 1048, :cpu => 1, :gui => false },
	{ :host => "bb-cli-2", :ip => "172.168.2.23", :box => "bento/centos-7.2", :ram => 1048, :cpu => 1, :gui => false },
]


# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.manage_guest = true
  config.hostmanager.ignore_private_ip = false
  config.hostmanager.include_offline = true


  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.

	all_nodes.each do |new_node|

		config.vm.define new_node[:host] do |new_node_config|

			new_node_config.vm.box = new_node[:box]
			#new_node_config.vm.box_version = "2.0.14"
			
			new_node_config.vm.network "private_network", ip: new_node[:ip], :netmask => "255.255.255.0"
			new_node_config.vm.hostname = "#{new_node[:host]}.#{VAR_DOMAIN}"
			# new_node_config.vm.hostname = new_node[:host] + "." + VAR_DOMAIN

		    new_node_config.hostmanager.aliases = "#{new_node[:host]}"

			new_node_config.vm.provider :vmware_desktop do |v|

				v.gui = new_node[:gui]

				v.vmx["displayName"] = new_node[:host].to_s
				#v.vmx[""] = new_node[:ram].to_s
				v.vmx["memsize"] = new_node[:ram].to_s
				v.vmx["numvcpus"] = new_node[:cpu].to_s
				v.vmx["svga.autodetect"] = "TRUE"

			end # end provider

			new_node_config.vm.synced_folder VAR_ROPOSITORY, "/repository", 
				id: "repository",
				owner: "vagrant",
				group: "vagrant"

			if new_node_config.vm.hostname == "jnk-svr-1" then
			#if "#{new_node[:host]}" == "jnk-svr-1" then
				puts "######## Checking for Jenkins Server..."
				puts "######## Jenkins Server Found. Running shell script..."

				# Add Google Chrome repository
				new_node_config.vm.provision :shell, inline: "wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub|sudo apt-key add -"
				new_node_config.vm.provision :shell, inline: "sudo sh -c 'echo \"deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main\" > /etc/apt/sources.list.d/google.list'"
				
				# Update repositories
				new_node_config.vm.provision :shell, inline: "sudo apt update -y"

				# Upgrade installed packages
				new_node_config.vm.provision :shell, inline: "sudo apt upgrade -y"

				# Add desktop environment
				new_node_config.vm.provision :shell, inline: "sudo apt install -y --no-install-recommends ubuntu-desktop"
				new_node_config.vm.provision :shell, inline: "sudo apt install -y --no-install-recommends virtualbox-guest-dkms virtualbox-guest-utils virtualbox-guest-x11"
				# Add `vagrant` to Administrator
				new_node_config.vm.provision :shell, inline: "sudo usermod -a -G sudo vagrant"
				
				# Add Google Chrome
				new_node_config.vm.provision :shell, inline: "sudo apt install -y google-chrome-stable"

				# Add Chromium
				new_node_config.vm.provision :shell, inline: "sudo apt install -y chromium-browser"

				# Add Firefox
				new_node_config.vm.provision :shell, inline: "sudo apt install -y firefox"


				# Install Jenkins
				new_node_config.vm.provision :shell, :path => "jenkins-install.sh"

				# Restart
				new_node_config.vm.provision :shell, inline: "sudo shutdown -r now"
			end
		end # end config
	end # end cluster
end
