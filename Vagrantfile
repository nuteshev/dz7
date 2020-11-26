# Describe VMs
MACHINES = {
  # VM name "kernel update"
  :"packages" => {
              # VM box
              :box_name => "centos/7",
              # VM CPU count
              :cpus => 1,
              # VM RAM size (Mb)
              :memory => 512,
              # networks
              :net => [],
              # forwarded ports
              :forwarded_port => [guest: 80, host: 8080]
            }
}

class FixGuestAdditions < VagrantVbguest::Installers::Linux
    def install(opts=nil, &block)
        communicate.sudo("yum update kernel -y; yum install -y gcc binutils make perl bzip2 kernel-devel kernel-headers", opts, &block)
        super
    end
end

Vagrant.configure("2") do |config|
  MACHINES.each do |boxname, boxconfig|
    # Disable shared folders
	config.vbguest.installer = FixGuestAdditions
    # Apply VM config
    config.vm.define boxname do |box|
      # Set VM base box and hostname
      box.vm.box = boxconfig[:box_name]
      box.vm.host_name = boxname.to_s
      # Additional network config if present
      if boxconfig.key?(:net)
        boxconfig[:net].each do |ipconf|
          box.vm.network "private_network", ipconf
        end
      end
      # Port-forward config if present
      if boxconfig.key?(:forwarded_port)
        boxconfig[:forwarded_port].each do |port|
          box.vm.network "forwarded_port", port
        end
      end
      # VM resources config
      box.vm.provider "virtualbox" do |v|
        # Set VM RAM size and CPU count
        v.memory = boxconfig[:memory]
        v.cpus = boxconfig[:cpus]
      end
	  box.vm.provision "shell", path: "prepare.sh"
    end
	
  end
end
