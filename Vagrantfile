# Specify minimum Vagrant version and Vagrant API version
Vagrant.require_version ">= 1.6.0"
VAGRANTFILE_API_VERSION = "2"
 
# Require YAML module
require 'yaml'


Vagrant.configure("2") do |config|
  
  boxes = [
    {
      :name => "jenkins_master",
      :box => "geerlingguy/debian9",
      :primary => true,
      :private_ip => "172.16.10.3"
    },
    {
      :name => "jenkins_slave",
      :box => "geerlingguy/debian9",
      :primary => false,
      :private_ip => "172.16.10.4"
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
          ansible.limit = "jenkins_farm"
          ansible.ask_vault_pass = false

          ansible.groups = {
            "jenkins" => ["jenkins_master"],
            "jenkins:vars" => YAML.load_file('./inventory/group_vars/jenkins.yml'),

            "jenkins_farm" => ["jenkins_master", "jenkins_slave"],
            "jenkins_farm:vars" => YAML.load_file('./inventory/group_vars/jenkins_farm.yml'),

            "jenkins_master" => ["jenkins_master"],
            "jenkins_master:vars" => YAML.load_file('./inventory/group_vars/jenkins_master.yml'),

            "jenkins_slaves" => ["jenkins_slave"],
            "jenkins_slaves:vars" => YAML.load_file('./inventory/group_vars/jenkins_slaves.yml')#,

            # "vagrant" => ["jenkins_master"],
            # "vagrant:vars" => YAML.load_file('./inventory/group_vars/vagrant.yml')
          }
        end
      end
    end
  end
end
