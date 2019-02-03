# Jenkins Farm playbook

An Ansible playbook to install and setup [Jenkins](https://jenkins.io), an automation server and some build slaves.


## Usage

1. copy `jenkins_farm.yml` to your master playbook directory (e.g.
`your-playbook/playbooks/jenkins_farm.yml`) and import:
  
  ```yaml
  # your-playbook/main.yml

    # ... 

    - import_playbook: playbooks/jenkins_farm.yml
  ```
2. declare your jenkins host:
   ```yaml
   # inventory/inventory.yml

   # ...
   jenkins_master:
     hosts:
       jenkins_master.example.com:
   
   jenkins_slaves:
     hosts:
       jenkins_node1.example.com:
       jenkins_node2.example.com:

   jenkins_farm:
     children:
       jenkins_master:
       jenins_slaves:
   ```
3. define the minimum variables (in inventory):
   ```yaml
   # inventory/group_vars/jenkins_master/vars_jenkins_master.yml

   ### role : dankempster.jenkins-config
   jenkins_farm_role: master

   ### role : geerlingguy.jenkins
   jenkins_hostname: "{{ jenkins_farm_master }}"
   jenkins_http_port: 8080

   jenkins_plugins: []
   ```

   ```yaml
   # inventory/group_vars/jenkins_master/vault_jenkins_master.yml
   #   should be encrypted with ansible-vault
   
   jenkins_admin_user: admin
   jenkins_admin_pass: admin
   ```
  
   ```yaml
   # inventory/group_vars/jenkins_farm/vars_jenkins_slaves.yml

   jenkins_farm_role: slave
   jenkins_home: /home/jenkins
   ```
  
   ```yaml
   # inventory/group_vars/jenkins_farm/vars_jenkins_farm.yml

   ### role : dankempster.jenkins-config
   jenkins_farm_master: jenkins_master.example.com
   ```
4. (optional) setup github credentials
   ```yaml
   # inventory/group_vars/jenkins_master/vars_github.yml
   
   ### role : dankempster.jenkins-config
   jenkins_github_enabled: true
   ```

   ```yaml
   # inventory/group_vars/jenkins_master/vault_github.yml
   #   should be encrypted with ansible-vault
   
   ### role : dankempster.jenkins-config
   jenkins_github_user: dankempster
   jenkins_github_token: SECRET_GITHUB_TOKEN
   ```
5. (optional) add jenkins jobs
   ```xml
   <!-- my-playbook/files/jenkins-jobs/myjob.xml -->
   <project>
     <description>My Job</description>
     <!-- ... -->
   </project>
   ```
   _Tip: Use JobDSL and Jenkins Playground to generate job XML._

   ```yaml
   # inventory/group_vars/jenkins_master/vars_jenkins_jobs.yml

   # paths should be relative to the jenkins_farm.yml playbook
   jenkins_jobs:
     - ../files/jenkins-jobs/myjob.xml
   ```
6. Run your playbook:
   ```
   $ ansible-playbook my-playbook/main.yml -i inventory/ -l jenkins
   ```


## Variables

This playbook defines no variables itself, however there are many variables defined by the dependant roles:

 - [geerlingguy.java](https://github.com/geerlingguy/ansible-role-java/blob/master/README.md#role-variables)
 - [geerlingguy.jenkins](https://github.com/geerlingguy/ansible-role-jenkins/blob/master/README.md#role-variables)


## Development

You will need [vagrant](https://www.vagrantup.com/downloads.html) installed, then simply `vagrant up`.

`vagrant provision` to provison an already `up`'d box, i.e. after making a change to the playbook.

`vagrant destroy` to, well, destroy the box, i.e. to prepare for running the playbook from scratch.
   

## Runnning the tests

This playbook comes with a few tests written using [Goss](https://github.com/aelsabbahy/goss). I use [molecule](https://molecule.readthedocs.io) to run these tests.

1. Install [docker](https://www.docker.com/products)
2. Install [python](https://www.python.org), usually preinstalled on most systems.
3. Install [python pip](https://pip.pypa.io/en/stable/installing/), if not installed along side python.
4. Install [molecule](https://molecule.readthedocs.io/en/latest/installation.html)
5. Install docker python package `pip install docker`, for molecule to interactive with docker.
6. Run the tests with `molecule test`


## Reusability

I endeavour to make my playbooks reusable, so that means defining minimal (preferrably no) variables. This is because variables defined in a playbook override host/group variables, and I define all my variables in inventory.

So, this playbook can be used in the knwoledge that it won't suddenly define varaibles that override your own.


## Credits

This playbook was created by [Dan Kempster](https://github.com/dankempster).

Special thanks to [Geerlingguy](https://github.com/geerlingguy) who maintains [geerlingguy.java](https://github.com/geerlingguy/ansible-role-java) & [geerlingguy.jenkins](https://github.com/geerlingguy/ansible-role-jenkins).

