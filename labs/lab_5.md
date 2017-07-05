# Lab 5 Setup a Chef Node

Pre-requisite Lab 4 

---

KEY POINT: Any computer that's managed by Chef is called a node.

---

For this lab I recommend opening a new terminal session dividing your screen by four, one corner for this instructions, one for the Chef Server, one for the workstation and finally one for the Chef Node that we will setup below.

## Get the Chef Server IP
You will need the Chef Server IP to setup connectivity between the Chef Node and Server. If you don't remember from the previous lab, you can get it by running the following command from either the Chef Workstation or the Chef Server:

```
cat /etc/hosts
```

Please take note of the ip in the line chef-server.


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
sudo docker run -ti -p 8082:80 --name chef-node -h chef-node ubuntu:14.04 bash 
```

Let's start connecting the Chef Node with the Chef Server by IP. Please add the Chef Server ip in the /etc/hosts file. In our case is:

```
172.17.0.2 chef-server
```
You can verify it's working by running:

```
ping chef-server
```


Once you sorted that out, you need to install a few packages:

```
apt-get update 
apt-get -y install openssh-server vim nano
service ssh restart
mkdir /root/.ssh
touch /root/.ssh/authorized_keys
```

Now we need to add the ssh public key from the workstation to /root/.ssh/authorized_keys. Please open the file with *vim* or *nano* and add the Chef Workstation public ssh key.

Finally, we need to take note of the Chef Node IP. Please run the following command:
```
cat /etc/hosts
```
In our case the ip was *172.17.0.4* We will use this ip below but please replace it accordingly. 

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

For this to work, you need the Chef Server to be accesible by name from both the chef_workstation and the node.
You also need ssh working from the chef workstation to the chef node.


## Troubleshooting:

If the command above failed, please go back to the instructions from the top of this page in case you missed something. Additionally you can try these:

* Ensure that you run knife commands from /root/chef_repo/cookbooks. 
* Ensure you have a /root/chef_repo/.chef directory and that it contains a knife.rb file and your RSA private key file. 
* Ensure that your node's IP address is accessible from the Chef Workstation. 
* Ensure the user name you provide has root or sudo access on the node. 
* Ensure your node provides network access on these ports: 
        22 (SSH) 
        80 (HTTP) 
        443 (HTTPS)

## Setup the Chef Node

To verify that your node was associated with your Chef server run the knife node list command:

```
knife node-list 
```

It should return:
```
node1-ubuntu 
```
If it did, congratulations! It was hard work but you did it ;)


Other commands you can try:

```
knife node show node1-ubuntu
```

This should output something like:

```
Node Name:   node1-ubuntu 
Environment: _default 
FQDN:        ae859505678f 
IP:          172.17.0.4
Run List:    recipe[learn_chef_apache2] 
Roles:       
Recipes:     learn_chef_apache2, learn_chef_apache2::default 
Platform:    ubuntu 14.04 
Tags:
```

To test the web server we just setup in the node:

```
curl 172.17.0.4 
```

It should output:
```
<html> 
<body> 
Hello Chef from a Template inside a Cookbook
<hr>
Node: chef_node
</body> 
</html> 
```
You can query the same page in your host browser going to http://vm_ip:8082



## About Knife Bootstrap

* You ran knife bootstrap to associate your node with the Chef server and do an initial check-in. Bootstrapping is a one-time process.
* The knife ssh command enables you to update your node's configuration when your cookbook changes.

Optional video: 
What happens during knife bootstrap?
https://learn.chef.io/modules/beyond-the-basics/what-happens-during-knife-bootstrap#/
