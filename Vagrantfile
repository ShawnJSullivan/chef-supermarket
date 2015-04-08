# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = '2'

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # berkshelf
  # config.berkshelf.berksfile_path = 'gannettdigital.berksfile'

  # settings for vagrant hostmanager
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = false
  config.hostmanager.ignore_private_ip = false
  config.hostmanager.include_offline = true

  (1..1).each do |i|
    config.vm.define "supermarket#{i}" do |node|
      # select which vm box to use
      node.vm.box = 'chef/centos-6.5'
      node.omnibus.chef_version = :latest
      node.vm.hostname = "supermarket#{i}"
      # configure network
      node.vm.network 'private_network', ip: "192.168.50.13#{i}"

      # override VM provider settings for memory and cpu
      node.vm.provider 'virtualbox' do |v|
        v.name = "supermarket#{i}"
        v.memory = 1024
        v.cpus = 2
      end

      # provision with chef
      node.vm.provision 'chef_zero' do |chef|
        chef.node_name = "supermarket#{i}"
        chef.log_level = :debug
        chef.cookbooks_path = "~/development/chef-repo/cookbooks"
        chef.data_bags_path = "~/development/chef-repo/data_bags"
        chef.roles_path = "~/development/chef-repo/roles"
        chef.add_role 'supermarket'
      end
    end
  end
end
