# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.vm.box = "debian/jessie64"

  config.vm.synced_folder "../", "/jepsen"

  # machine for running jepsen tests
  config.vm.define :jepsen do |mxconsole|
    mxconsole.vm.hostname = "jepsen"

    mxconsole.vm.provision :shell, :path => "setup.sh"
    mxconsole.vm.provision :shell, :path => "keys.sh"
    mxconsole.vm.provision :shell, :path => "net.sh"
    mxconsole.vm.provision :shell, :path => "lxc.sh"
  end

end
