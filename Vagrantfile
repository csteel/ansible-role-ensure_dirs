# -*- mode: ruby -*-
# vi: set ft=ruby ts=2 sw=2 tw=0 et :

role = File.basename(File.expand_path(File.dirname(__FILE__)))

boxes = [
#  {
#    :name => "ubuntu-1204",
#    :box => "bento/ubuntu-12.04",
#    :ip => '10.0.0.11',
#    :cpu => "50",
#    :ram => "256"
#  },
#  {
#    :name => "ubuntu-1404",
#    :box => "bento/ubuntu-14.04",
#    :ip => '10.0.0.12',
#    :cpu => "50",
#    :ram => "256"
#  },
  {
    :name => "ubuntu1604",
    :box => "ubuntu/xenial64",
#    :box => "bento/ubuntu-16.04",
    :ip => '10.0.0.13',
    :cpu => "50",
    :ram => "256"
  },
#  {
#    :name => "debian-711",
#    :box => "bento/debian-7.11",
#    :ip => '10.0.0.14',
#    :cpu => "50",
#    :ram => "256"
#  },
#  {
#    :name => "debian-86",
#    :box => "bento/debian-8.6",
#    :ip => '10.0.0.15',
#    :cpu => "50",
#    :ram => "256"
#  },
]

Vagrant.configure("2") do |config|
  boxes.each do |box|
    config.vm.define box[:name] do |vms|
      vms.vm.box = box[:box]
      vms.vm.hostname = "ansible-#{role}-#{box[:name]}"
#      vms.vm.synced_folder ".vagrant/synced", "/home/vagrant"
      vms.vm.provider "virtualbox" do |v|
        v.customize ["modifyvm", :id, "--cpuexecutioncap", box[:cpu]]
        v.customize ["modifyvm", :id, "--memory", box[:ram]]
      end

      vms.vm.network :private_network, ip: box[:ip]

      vms.vm.provision "shell",
        inline: "sudo apt-get update && sudo apt-get install -y python"


      vms.vm.provision :ansible do |ansible|
        ansible.playbook = "tests/vagrant.yml"
        ansible.verbose = "vv"
      end
    end
  end
end
