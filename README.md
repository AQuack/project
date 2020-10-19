# project
# Context and background:
.gitignore - tells github what to ignore
ansible.cfg - all that's here is defaults, telling ansible where the inventory file is, and supressing complaints about python intreperter


# Breaking down the playbook
The steps for the playbook are: Create a temp folder, copy the agent over, run the install script

# Create a temp folder:
#We put a temp folder in /tmp/ called carbonblack. This is just to have a consistent space that to put the agent.

# Copy the agent over
We just extract the agent over the network to our /tmp/carbonred folder. Please note that the agent sits in the files folder, should be files/<policyname>-redhat.tgz
In our playbook, the linux agent was named InstallUninstall policy-redhat.tgz. If your agent name is different, update the playbook on lines 11, 13, and 18

Line 13 tells ansible that "Copy agent over" creates the /tmp/carbonred/InstallUninstall policy-redhat folder. This lets ansible know to see if the folder exists, if it does
ansible will not create it again. If your <policyname>-redhat.tgz isn't InstallUninstall, you'll want to update this name to reflect your policy name

# Run the installer:
here we just tell ansible to launch a shell in /tmp/carbonblack/InstallUninstall policy-rehat, and run the b9install.sh while supressing the b9notifier.
we tell ansible that this action creates /srv/bit9, so it checks to see if this exist, if it does, ansible won't attempt to run b9install.sh
