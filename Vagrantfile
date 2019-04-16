# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.network "forwarded_port", guest: 3000, host: 3000
  config.vm.network "forwarded_port", guest: 9090, host: 9090
  boxes = [
#    { :name => "ubuntu-hello-world-box", :box => "kbi/ubuntu16.04" },
#    { :name => "centos-hello-world-box", :box => "kbi/centos7" },
#    { :name => "centos-hello-world-box2", :box => "centos7-ansible" },
    { :name => "centos7-ansible", :box => "centos7-ansible" }
  ]
  boxes.each do |opts|
    config.vm.define opts[:name] do |config|
      config.vm.box = opts[:box]
      if opts[:name] == boxes.last[:name]
        config.vm.provision "ansible" do |ansible|
          ansible.playbook = "site.yml"
          ansible.limit = "all"
#          ansible.verbose = "vvv"
        end
      end
    end
  end
end
