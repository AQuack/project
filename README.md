# Context and background:
Ansible playbook to deploy app control agents to linux machines

## Requirements:
Ansible
sudo password for account used to execute ansible tasks
App control server
Linux host package

## Breakdown of tasks
The steps for the playbook are: Create a temp folder, copy the agent over, run the install script
### Create a temp folder:
```ansible
- name: make temp folder
    file:
      path: /tmp/carbonblack
      state: directory
```
We put a temp folder in /tmp/ called carbonblack. This is just to have a consistent space that to put the agent.

### Copy the agent over
```ansible
  - name: Copy agent over
    unarchive: 
      src: files/installuninstall policy-redhat.tgz
      dest: /tmp/carbonblack
      creates: /tmp/carbonblack/installuninstall policy-redhat
  ```
We just extract the agent over the network to our /tmp/carbonred folder. Please note that the agent sits in the files folder, should be files/<policyname>-redhat.tgz
In our playbook, the linux agent was named InstallUninstall policy-redhat.tgz. 
  
 ***If your agent name is different, update the playbook on lines 11, 13, and 18***

Line 13 tells ansible that "Copy agent over" creates the /tmp/carbonred/InstallUninstall policy-redhat folder. This lets ansible know to see if the folder exists, if it does
ansible will not create it again. If your <policyname>-redhat.tgz isn't InstallUninstall, you'll want to update this name to reflect your policy name

### Run the installer:
```ansible
  - name: Run the install
    become: true
    shell: sh ./b9install.sh -n
    args:
      chdir: "/tmp/carbonblack/InstallUninstall policy-redhat"
      creates: /srv/bit9
 ```
here we just tell ansible to launch a shell in /tmp/carbonblack/InstallUninstall policy-rehat, and run the b9install.sh while supressing the b9notifier.
we tell ansible that this action creates /srv/bit9, so it checks to see if this exist, if it does, ansible won't attempt to run b9install.sh

# Prepare and Run the playbook

### Required files
**inventory/hosts.ini** to tell ansible what hosts we want to deploy too. Update the [sensors] field with the sensors you wish to deploy sensors to

**files/** as the place to put our linux agent (still zipped) the playbook is looking for "InstallUninstall Policy" but this can be edited, refer to the "breakdown of tasks section"

**install.yaml** is the playbook

### Steps to run
1. Put the linux package in the files/ folder. We are expecting files/installuninstall policy-redhat.tgz. If yours is different, update the playbook as stated above
2. Update the hosts.ini file to reflect desired hosts deploying to under [sensors], update [control] to reflect actual control node, and remove the [sensors:children] update if not requres (in it's current state, the playbook deploys linux agents to sensors, and it's own control node, this may not be required in your environment)
3. from inside the project folder, run ```ansible ansible-playbook install.yaml --ask-become-pass``` ask-become-pass requires the sudo password for running the b9install.sh as root at the remote machine
4. Review output of playbook
5. Check console, sensors should begin checking in if playbook completed successfully
