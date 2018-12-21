Vagrant.configure("2") do |config|
  
  boxes = [
    {
      :name => "jenkins",
      :hostname => "jenkins.playbook.ansible.test",
      :box => "geerlingguy/debian9",
      :private_ip => "172.16.0.3"
    }
  ] 

  boxes.each do |opts|
    config.vm.define opts[:name] do |config|
      
      config.vm.box = opts[:box]
      config.vm.hostname = opts[:hostname]
      config.vm.network :private_network, ip: opts[:private_ip]

      config.vm.synced_folder "./vagrant-jenkins-home/", "/opt/jenkins-git-copy",
        owner: "vagrant",
        group: "vagrant"
      
      if opts[:name] == boxes.last[:name]
        config.vm.provision "ansible" do |ansible|
          ansible.playbook = "playbook.yml"
          # ansible.limit = "all"
          ansible.ask_vault_pass = false
        end
      end

    end
  end
end
