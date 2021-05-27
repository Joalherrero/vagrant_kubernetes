Vagrant.configure("2") do |config|
    config.ssh.insert_key = false

    config.vm.provider "virtualbox" do |v|
        v.memory = 2048
        v.cpus = 2
    end


    config.vm.define "ansible" do |ansible|
        ansible.vm.box = "bento/ubuntu-16.04"
        ansible.vm.box_version = "202102.02.0"
        ansible.vm.network "private_network", ip: "203.0.113.8"
        ansible.vm.hostname = "ansible-slave"
        ansible.vm.synced_folder "ansible_vagrant", "/ansible_vagrant", :mount_options => ["ro"]
        ansible.vm.provision "ansible_local" do |ansible|
            ansible.provisioning_path = "/ansible_vagrant/vagrant/provisioning/"
            ansible.inventory_path = "inventory"
            ansible.playbook = "ansible-playbook.yml"
            ansible.limit = "all"
        end
    end
 
      
    config.vm.define "k8s-master" do |master|
        master.vm.box = 'bento/ubuntu-16.04'
        master.vm.box_version = "202102.02.0"
        master.vm.network "private_network", ip: "203.0.113.110"
        master.vm.network "forwarded_port", guest: 8080, host: 8080, auto_correct:false
        master.vm.hostname = "k8s-master"
        master.vm.synced_folder "ansible_vagrant", "/ansible_vagrant/vagrant/provisioning", :mount_options => ["ro"]
        master.vm.provision "ansible_local" do |ansible|
            ansible.provisioning_path = "/vagrant/ansible_vagrant/vagrant/provisioning/"
            ansible.inventory_path = "inventory"
            ansible.playbook = "master-playbook.yml"
            ansible.extra_vars = {
                node_ip: "203.0.113.110",
            }
        end
    end

    (1..3).each do |i|
        config.vm.define "k8s-node-#{i}" do |node|
            node.vm.box = 'bento/ubuntu-16.04'
            node.vm.box_version = "202102.02.0"
            node.vm.network "private_network", ip: "203.0.113.#{i + 10}"
            node.vm.hostname = "node-#{i}"
            node.vm.synced_folder "ansible_vagrant", "/ansible_vagrant/vagrant/provisioning", :mount_options => ["ro"]
            node.vm.provision "ansible_local" do |ansible|
                ansible.provisioning_path = "/vagrant/ansible_vagrant/vagrant/provisioning/"
                ansible.inventory_path = "inventory"
                ansible.playbook = "node-playbook.yml" 
                ansible.extra_vars = {
                    node_ip: "203.0.113.#{i + 10}",
                }
            end
        end
    end
end
