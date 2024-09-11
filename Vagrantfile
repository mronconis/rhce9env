# network
IP_NW="192.168.51."
IP_START_SERVER=50
SERVER_REPLICAS=2

Vagrant.configure("2") do |config|
  config.vm.box = "centos/7"

  (1..SERVER_REPLICAS).each do |machine_id|
    config.vm.define "server#{machine_id}", primary: true do |machine|
      machine.vm.hostname = "server#{machine_id}.example.redhat.com"
      machine.vm.network "private_network", ip: "#{IP_NW}#{IP_START_SERVER+machine_id}"
      machine.vm.network "private_network", ip: "10.0.0.#{IP_START_SERVER+machine_id}"
      
      machine.vm.provider :virtualbox do |vb|
        file_to_disk1 = "box_disk1-server#{machine_id}.vdi"
        unless File.exist?(file_to_disk1)
          vb.customize ['createhd', '--filename', file_to_disk1, '--size', 1024]
        end
        vb.customize ['storageattach', :id, '--storagectl', 'IDE', '--port', 1, '--device', 0, '--type', 'hdd', '--medium', file_to_disk1]  
      end

      machine.vm.provider :virtualbox do |vb|
        file_to_disk2 = "box_disk2-server#{machine_id}.vdi"
        unless File.exist?(file_to_disk2)
          vb.customize ['createhd', '--filename', file_to_disk2, '--size', 5 * 1024]
        end
        vb.customize ['storageattach', :id, '--storagectl', 'IDE', '--port', 1, '--device', 1, '--type', 'hdd', '--medium', file_to_disk2]  
      end

      machine.vm.provision "shell", inline: <<-SHELL
        # Fix repo uri
        sed -i s/mirror.centos.org/vault.centos.org/g /etc/yum.repos.d/*.repo
        sed -i s/^#.*baseurl=http/baseurl=http/g /etc/yum.repos.d/*.repo
        sed -i s/^mirrorlist=http/#mirrorlist=http/g /etc/yum.repos.d/*.repo
      SHELL
  
      if machine_id == SERVER_REPLICAS
        machine.vm.provision :ansible do |ansible|
          #ansible.verbose = "vvv"
          ansible.compatibility_mode = "2.0"
          ansible.limit = "all"
          ansible.playbook = "playbooks/site.yml"

          ansible.host_vars = {
            "server1" => {
              "id" => "d_1"},
            "server2" => {
              "id" => "p_2"}
          }

          ansible.groups = {
            "dev" => ["server1"],
            "prod" => ["server2"]
          }
        end
      end

    end
  end

  config.vm.provider "virtualbox" do |vb|
    vb.memory = "1024"
    vb.cpus = 1
  end

end
