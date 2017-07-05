# Lab 5 Setup a Chef Node

Pre-requisite Lab 4 

---

KEY POINT: Any computer that's managed by Chef is called a node.

---

For this lab I recommend opening a new terminal session dividing your screen by four, one corner for this instructions, one for the Chef Server, one for the workstation and finally one for the Chef Node that we will setup below.

## Setup ssh keys in your Chef Workstation 
We need to setup the authentication method from the Chef Workstation to the Chef Node. To do so, please run this command, hitting Enter to get the default options.

```
ssh-keygen -N ''
cat ~/.ssh/id_rsa.pub
```
The output of the last command is the ssh public key of the workstation. It will look something like:

    ssh-rsa                 AAAAB3NzaC1yc2EAAAADAQABAAABAQDQO9eteye/KDwUjUPerYt3ik8DcM9lWnDBSDQlijGF0ElFgqU1uc33paF7KZvyOKF6ex5fFChsy00Z0tJNZbEQ8uV7Z9FIcCmgsr3KerkmIfAhJi0qiRPndU4o0myFnOaC274bV9CXIU3Thj4EG6Tm+IjWSTYtc1ep4yQc8upUgPypLfySe2WCnK1H5XRcsqf2DJQmf4L+zEVhhNTkoM1l1QjBR3gxo0fywtXg5rtPuBmk9SHpui7ureGAldmJpoT1GzdovxOmbkOg6Ro6E1/dN333jLwBM0nAn1FQMRz2QjPxdQ+MVN8uiKq4PYWxFxlriqb6weN+T20LyLM9XAh1 adrian@adrian- ubuntu-XPS

By selecting it in Putty, you will copy it automatically. We will paste it below shortly.


## Starting your Chef Node

Create a new node machine

```
sudo docker run -ti --name chef_node -h chef_node ubuntu:14.04 bash 
```

Once inside the container, please run:

```
apt-get update 
apt-get -y install openssh-server vim nano
service ssh restart
mkdir /root/.ssh
touch /root/.ssh/authorized_keys
```

```
ssh-keygen -N ''
```

Now we need to add the ssh public key from the workstation to /root/.ssh/authorized_keys. Please open the file with *vim* or *nano* and add the Chef Workstation public ssh key.

Finally, we need to take note of the Chef Node IP. Please run the following command:
```
cat /etc/hosts
```
In our case the ip was *172.17.0.4* We will use this ip below but please replace in the commands below accordingly. 

## Verifying connectivity

Now go to the Chef Workstation and run the command below. Remember to change the Chef Node ip by your own:

```
ssh 172.17.0.4 "ls /root/.ssh"
```

If you didn't get any error but a list of files, you are good to go!


## Bootstraping your node 

Any computer that's managed by a Chef server is called a node, and that chef-client is the command that applies the policy defined by your cookbooks to a node. We call the process that installs chef-client on a node and the initial check-in to the Chef server bootstrapping the node.

In this part you'll bootstrap your node and execute the learn_chef_apache2 cookbook on it.
*knife bootstrap* is the command you use to bootstrap a node. As part of the knife bootstrap command, you specify arguments depending on how you would normally connect to your node over SSH.

Go to your Chef Workstation session and run the command above. Please remeber to replace the 172.17.0.4 ip with the one from your own Chef Node:

```
cd /root/chef_repo/cookbooks
knife bootstrap 172.17.0.4 --ssh-user root --sudo --identity-file ~/.ssh/id_rsa --node-name node1-ubuntu --run-list 'recipe[learn_chef_apache2]' 
```

For this to work, you need the chef_server to be accesible by name from both the chef_workstation and the node.
You also need ssh working from the chef workstation to the chef node.

Troubleshooting:
If the operation times out or fails, here are some things to try. 

    Ensure that you run knife commands from your learn-chef directory or one of its sub-directories. 
    Ensure you have a learn-chef/.chef directory and that it contains a knife.rb file and your RSA private key file. 
    Ensure that your node's IP address is accessible from your network. 
    Ensure the user name you provide has root or sudo access on the node. 
    Ensure your node provides network access on these ports: 
        22 (SSH) 
        80 (HTTP) 
        443 (HTTPS)

What happens during knife bootstrap?
https://learn.chef.io/modules/beyond-the-basics/what-happens-during-knife-bootstrap#/

First, your node was associated with your Chef server. To verify this, run the knife node list command.
# knife node-list 
node1-ubuntu 

# knife node show node1-ubuntu 
Node Name:   node1-ubuntu 
Environment: _default 
FQDN:        ae859505678f 
IP:          172.17.0.4
Run List:    recipe[learn_chef_apache2] 
Roles:       
Recipes:     learn_chef_apache2, learn_chef_apache2::default 
Platform:    ubuntu 14.04 
Tags:

From the node:
root@ae859505678f:~# curl localhost 
<html> 
  <body> 
    <h1>hello world</h1> 
  </body> 
</html> 


Which of the following most completely describes what happens during the bootstrap process? 

    The Chef client is installed on the node. 
    The node checks in with the Chef server for the first time. 
X    The Chef client is installed on the node and the node then checks in with the Chef server. 

Which argument to the knife bootstrap command specifies your node's run-list? 

X    --run-list 
    --run_list 
    --cookbooks 
  

Update your node's configuration 

- You ran knife bootstrap to associate your node with the Chef server and do an initial check-in. Bootstrapping is a one-time process.
- The knife ssh command enables you to update your node's configuration when your cookbook changes.

