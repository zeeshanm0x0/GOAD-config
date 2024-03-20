Vagrant.configure("2") do |config|
  # Set the default provider
  config.vm.provider :vmware_esxi do |esxi|
    # ESXi server details
    esxi.esxi_hostname = "ESXI-IP"
    esxi.esxi_username = "ESXI-USERNAME"
    esxi.esxi_password = "ESXI-PASSWORD"
  end
  
  # Define the configuration for each Windows Server 2019 machine
  [
    { name: "GOAD-DC01", ip: "192.168.47.10" },
    { name: "GOAD-DC02", ip: "192.168.47.11" },
    { name: "GOAD-SRV02", ip: "192.168.47.12" }
  ].each do |machine|
    config.vm.define machine[:name] do |windows|
      # Box settings
      windows.vm.box = "StefanScherer/windows_2019"
      windows.vm.box_version = ">= 2021.05.15"
      
      # Network settings
      windows.vm.network "private_network", ip: machine[:ip]
      
      # Guest OS configuration
      windows.vm.guest = :windows
      windows.vm.communicator = "winrm"

      # Set the name of vmware-instance on ESXI example GOAD-DC01.tl-m
      windows.vm.hostname = "#{machine[:name]}.tl-m"
               
      # Provisioning scripts
      windows.vm.provision :shell, :path => "../../../../vagrant/Install-WMF3Hotfix.ps1", privileged: false
      windows.vm.provision :shell, :path => "../../../../vagrant/ConfigureRemotingForAnsible.ps1", privileged: false
      
      if ENV['VAGRANT_DEFAULT_PROVIDER'] == "vmware_esxi"
        windows.vm.provision :shell, :path => "../../../../vagrant/fix_ip.ps1", privileged: false, args: machine[:ip]
      end
      
      # Forwarded ports
      if machine.has_key?(:forwarded_port)
        machine[:forwarded_port].each do |forwarded_port|
          windows.vm.network :forwarded_port, guest: forwarded_port[:guest], host: forwarded_port[:host], host_ip: "127.0.0.1", id: forwarded_port[:id]
        end
      end
      
      # Additional provisioning scripts or configurations if needed
      # windows.vm.provision "shell", path: "additional_provision_script.sh"
    end
  end
end
