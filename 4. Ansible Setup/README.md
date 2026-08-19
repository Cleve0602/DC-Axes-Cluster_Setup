# Ansible Setup

Ansible is a powerful configuration management tool used for automating the deployment, configuration, and management of software systems. It allows you to control many different systems from one central location.


1. Install Ansible on your head node (it is not required on your compute node)
   * DNF / YUM
   ```bash
   # RHEL, Rocky, Alma, CentOS Stream
   sudo dnf install epel-release
   sudo dnf install python ansible
   ```

   1. Configure your inventory file

   Setup an Ansible inventory file which contains a list of nodes or *hosts*, that you will be managing.
   * Open a file in your `/home` directory
   ```bash
   vi ~/inventory
   ```

   ```ini
   [head]
   # Only the head node's IP or hostname, when you need stricly management tasks
   192.168.15.1

   [compute]
   # List of all of your COMPUTE nodes
   # Depending on your cluster design, your head node may also be compute
   192.168.15.2
   ```

   Test to see if your Ansible control host can access all nodes listed in the inventory file

   ```bash
   # access as a group
   ansible -i inventory compute -m ping

   #access as an individual host
   ansible -i inventory 192.168.15.0 -m ping

   #run command on hosts
   ansible -i inventory compute -m shell -a 'free -m'
   ```


   ## How to Use Ansible
   Create an Ansilble working directory in your user's `/home`, to house your playbooks

   ```bash
   # Create the ansible playbooks directory
   mkdir -p ~/playbooks

   # Creating the sudo users ansible playbook script
   vi ~/playbooks/scriptexample.yml
   add whatever you want inside this script
      ```

    Run the playbook

   ```bash
   ansible-playbook -i inventory ~/playbooks/scriptexample.yml
   ```
