# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = '2'
VM_MEMORY = ENV['VM_MEMORY'] || '4096'
VM_CPUS = ENV['VM_CPUS'] || '2'
VM_NFS = (!ENV['VM_NFS'].nil? && %w(1 true yes).include?(ENV['VM_NFS']))
VM_PORT = (ENV['PORT'] || '3000').to_i

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.omnibus.chef_version = :latest
  config.vm.hostname = "vagrant-test-haproxy"

 
  config.vm.network :forwarded_port, guest: 3000, host: VM_PORT
  config.vm.network :forwarded_port, guest: 80, host: 8080
  #in the above line I forward port 80 despite not understanding WHY supermarket runs on port 80 and not 3000
  config.vm.synced_folder './', '/supermarket', nfs: VM_NFS
  config.vm.synced_folder './', '/vagrant', disabled: true

  config.vm.provider :virtualbox do |vb, override|
    config.vm.box     = "chef/centos-6.5"

    vb.customize ['modifyvm', :id, '--memory', VM_MEMORY]
    vb.customize ['modifyvm', :id, '--cpus', VM_CPUS]
  end

  config.vm.provider :vmware_fusion do |vmf, override|
    override.vm.box     = 'precise64'
    override.vm.box_url = 'http://files.vagrantup.com/precise64_vmware.box'
    vmf.vmx['memsize']  = VM_MEMORY
    vmf.vmx['numvcpus'] = VM_CPUS
  end

  config.vm.provision :chef_solo do |chef|
    chef.cookbooks_path = "~/chef-repo/cookbooks"
    chef.data_bags_path = "~/chef-repo/data_bags"

    chef.formatter = 'doc'
    chef.log_level = :warn

    chef.json = {
      postgresql: {
        user: 'vagrant',
        database: 'supermarket_development',
        auth_method: 'trust',
        password:{
          postgres:"password"
        }
      },
      supermarket: {
        host: 'localhost',
        port: VM_PORT
#        port: 8080
      }
    }

    chef.run_list = [
      'recipe[supermarket::default]'
    ]
  end
end