# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

cluster = {
  "ipa.test.local" => { :ip => "192.168.121.100", :cpus => 1, :mem => 3072, :boxname => "centos/7", :provider => "virtualbox", :playbook => "provisioners/playbook_ipa.yml"},
  "zookeeper1.test.local" => {  :ip => "192.168.121.21", :cpus => 1, :mem => 1024, :boxname => "bento/centos-7.9", :provider => "virtualbox", :playbook => "provisioners/playbook_zookeeper.yml"},
  "zookeeper2.test.local" => {  :ip => "192.168.121.22", :cpus => 1, :mem => 1024, :boxname => "bento/centos-7.9", :provider => "virtualbox", :playbook => "provisioners/playbook_zookeeper.yml"},
  "zookeeper3.test.local" => {  :ip => "192.168.121.23", :cpus => 1, :mem => 1024, :boxname => "bento/centos-7.9", :provider => "virtualbox", :playbook => "provisioners/playbook_zookeeper.yml"},
#  "kafka1.test.local" => { :ip => "192.168.121.10", :cpus => 1, :mem => 3072, :boxname => "bento/centos-7.9", :provider => "virtualbox", :playbook => "provisioners/playbook.yml"},
#  "kafka2.test.local" => {  :ip => "192.168.121.11", :cpus => 1, :mem => 3072, :boxname => "bento/centos-7.9", :provider => "virtualbox", :playbook => "provisioners/playbook.yml"},
#  "kafka3.test.local" => {  :ip => "192.168.121.12", :cpus => 1, :mem => 3072, :boxname => "bento/centos-7.9", :provider => "virtualbox", :playbook => "provisioners/playbook.yml"},
  "monitor.test.local" => {  :ip => "192.168.121.15", :cpus => 1, :mem => 1024, :boxname => "bento/centos-7.9", :provider => "virtualbox", :playbook => "provisioners/playbook_monitor.yml"}
}
 
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  cluster.each_with_index do |(hostname, info), index|
    config.hostmanager.enabled = false
    
    config.vm.define hostname do |cfg|
      
      cfg.vm.hostname = hostname
      cfg.hostsupdater.aliases = [hostname]
      cfg.vm.box = "#{info[:boxname]}" || 'virtualbox'
      
      cfg.vm.provider :"#{info[:provider]}" do |prov|
        prov.memory = info[:mem] || '512'
        prov.cpus = info[:cpus] || '1'
        prov.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
        prov.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
        prov.customize ["modifyvm", :id, "--ioapic", "on"]
      end # end provider
      
      unless info[:ip].nil?
        cfg.vm.network "private_network", ip: "#{info[:ip]}" 
      else
        cfg.vm.network "private_network", type: "dhcp"
      end 
      unless info[:forwarded_port_host].nil? || info[:forwarded_port_guest].nil?
        cfg.vm.network "forwarded_port", guest: "#{info[:forwarded_port_guest]}", host: "#{info[:forwarded_port_host]}"
      end
      # end network 

      

      unless info[:script].nil?
        cfg.vm.provision "shell" do |shell|
          shell.path  = "#{info[:script]}"
        end 
      end # end shell provisioner
      
      unless info[:playbook].nil?
        cfg.vm.provision "ansible" do |ansible|
          ansible.playbook  = "#{info[:playbook]}"
        end 
      end # end Ansible provisioner

      
    
    end # end config
  config.vm.provision :hostmanager
  end # end cluster
end
