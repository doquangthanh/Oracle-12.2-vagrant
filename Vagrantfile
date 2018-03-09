# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "ol7-latest"
  config.vm.box_url = "http://yum.oracle.com/boxes/oraclelinux/latest/ol7-latest.box"
  
  config.vm.box_check_update = false
  
  # change memory size
  config.vm.provider "virtualbox" do |v|
    v.memory = 2048
    v.name = "oracle12c-vagrant"
  end

  # Oracle port forwarding
  config.vm.network "forwarded_port", guest: 1521, host: 1521
  config.vm.network "forwarded_port", guest: 5500, host: 5500
  config.vm.network "public_network", ip: "10.9.11.251"

  # Provision everything on the first run
  config.vm.provision "shell", path: "scripts/install.sh", env:
    {
       "ORACLE_BASE"         => "/opt/oracle",
       "ORACLE_HOME"         => "/opt/oracle/product/12.2.0.1/dbhome_1",
       "ORACLE_SID"          => "ORCLCDB",
       "ORACLE_PDB"          => "ORCLPDB1",
       "ORACLE_CHARACTERSET" => "AL32UTF8"
    }

end
