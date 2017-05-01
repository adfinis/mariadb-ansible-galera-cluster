# -*- mode: ruby -*-
# vi: set ft=ruby :

CLUSTER_SIZE = 3

Vagrant.configure("2") do |config|
  config.vm.box = "centos/7"

  (1..CLUSTER_SIZE).each do |box_id|
    config.vm.define "galera-db-#{box_id}" do |box|
      box.vm.hostname = "galera-db-#{box_id}"
      box.vm.network "private_network", ip: "172.27.47.#{20+box_id}"
      config.vm.network "forwarded_port", guest: 3306, host: 3306,
        auto_correct: true

      # Only run Ansible once all boxes are up
      if box_id == CLUSTER_SIZE
        box.vm.provision :ansible do |ansible|
          # Disable default limit to connect to all the boxes
          ansible.limit = "all"
          ansible.sudo = true
          ansible.playbook = "galera.yml"
          ansible.groups = {
            "galera_cluster" => ["galera-db-1", "galera-db-2", "galera-db-3"]
          }
        end
      end
    end
  end
end
