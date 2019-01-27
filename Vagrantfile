# Specify minimum Vagrant version and Vagrant API version
Vagrant.require_version ">= 1.6.0"
VAGRANTFILE_API_VERSION = "2"
 
# Require YAML module
require 'yaml'


Vagrant.configure("2") do |config|
  
  boxes = [
    {
      :name => "jenkins_host",
      :box => "geerlingguy/debian9",
      :primary => true
    }
  ] 

  boxes.each do |opts|
    
    opts[:primary] = opts.key?(:primary) ? opts[:primary] : false

    config.vm.define opts[:name], primary: opts[:primary] do |config|
      
      config.vm.box = opts[:box]
      
      if (opts.key?(:hostname))
        config.vm.hostname = opts[:hostname]
      end
      
      if (opts.key?(:private_ip))
        config.vm.network :private_network, ip: opts[:private_ip]
      end

      # config.vm.synced_folder "./vagrant-jenkins-home/", "/opt/jenkins-git-copy",
      #   owner: "vagrant",
      #   group: "vagrant"
      
      if opts[:name] == boxes.last[:name]
        config.vm.provision "ansible" do |ansible|
          ansible.playbook = "playbook.yml"
          # ansible.limit = "all"
          ansible.ask_vault_pass = false

          ansible.groups = {
            "jenkins" => ["jenkins_host"],
            "jenkins:vars" => YAML.load_file('./inventory/group_vars/jenkins.yml'),

            "vagrant" => ["jenkins_host"],
            "vagrant:vars" => YAML.load_file('./inventory/group_vars/vagrant.yml')
          }
        end
      end
    end
  end
end
