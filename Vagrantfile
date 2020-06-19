domain = "test.com"
bridge_net = "192.168.33."

nodes = [
  {
    :hostname => "vm1." + domain,
    :ip => bridge_net + "51",
    :hdd_size => "20GB",
    :ram => "1024"
  },
  {
    :hostname => "vm2." + domain,
    :ip => bridge_net + "52",
    :hdd_size => "20GB",
    :ram => "1024"
  },
  {
    :hostname => "vm3." + domain,
    :ip => bridge_net + "53",
    :hdd_size => "20GB",
    :ram => "1024"
  }
]

Vagrant.configure("2") do |config|
  nodes.each do |node|
    config.vm.define node[:hostname] do |nodeconfig|
      nodeconfig.vm.box = "generic/centos8"
      nodeconfig.vm.hostname = node[:hostname]

      if (node[:hostname] == "vm3.test.com")
        nodeconfig.vm.provision "ansible" do |ansible|
          ansible.limit = "all"
          ansible.playbook = "provisioning/playbook.yml"
          #ansible.inventory_path = "provisioning/inventory"
          ansible.become = true
          ansible.groups = {
            "webservers" => ["vm1.test.com"],
            "gitservers" => ["vm2.test.com"],
            "dbservers" => ["vm3.test.com"],
            "all_groups:children" => ["webservers", "gitservers", "dbservers"],
            }
        end
      end

      nodeconfig.vm.network "private_network", ip: node[:ip],
        vitrualbox__intnet: true

      if (node[:hostname] == "vm1.test.com")
        nodeconfig.vm.network "forwarded_port", guest: 443, host: 8443, protocol: "tcp",
          auto_correct: true
      end

      nodeconfig.vm.provider "virtualbox" do |vb|
        vb.customize [
          "modifyvm", :id,
          "--groups", "/vagrant_test",
          "--cpuexecutioncap", "50",
          "--memory", node[:ram]
        ]
        vb.name = node[:hostname]
        vb.cpus = 1
      end
    end
  end
end
