# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.define "big-ip01.internal" do | v |
    v.vm.box = "BIGIP-11.6.0.0.0.401.LTM_1SLOT-ide.box"

    config.vm.synced_folder ".", "/vagrant", disabled: true

    v.vm.network :forwarded_port, guest: 443, host: 10443

    v.vm.provider :virtualbox do |p|
      p.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      p.customize ["modifyvm", :id, "--memory", 2048]
      p.customize ["modifyvm", :id, "--cpus", 2]
      p.customize ["modifyvm", :id, "--name", "big-ip01.internal"]

      p.customize ["modifyvm", :id, "--nic1", "nat"]
      p.customize ["modifyvm", :id, "--nictype1", "virtio"]
      p.customize ["modifyvm", :id, "--nic2", "hostonly"]
      p.customize ["modifyvm", :id, "--nictype2", "virtio"]
      p.customize ["modifyvm", :id, "--hostonlyadapter2", "VirtualBox Host-Only Ethernet Adapter #2"]
    end

      v.vm.provision "shell", inline: <<-SHELL
       while true; do
          string=`tmsh show sys failover`
          if [[ $string == *"active"* ]]
          then
              break
          else
              echo "runlevel is 'unknown' - waiting for 10s"
              sleep 10
          fi
       done
      echo "runlevel is now valid, kicking off provisioning..."
      tmsh load /sys config default
      tmsh modify sys db setup.run value false
      sleep 10
      /usr/local/bin/SOAPLicenseClient --basekey HYRFG-VDSVP-QBCVH-YEPEX-DGVEBOV
      sleep 10
      tmsh save sys config
    SHELL
    end
end