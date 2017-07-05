# Lab 5 Setup a Chef Node

Pre-requisite Lab 4 

---

KEY POINT: Any computer that's managed by Chef is called a node.

---

For this lab I recommend opening a new terminal session dividing your screen by four, one corner for this instructions, one for the Chef Server, one for the workstation and finally one for the Chef Node that we will setup below.

## Starting your Chef Node

Create a new node machine

```
sudo docker run -ti --name chef_node -h chef_node ubuntu:14.04 bash 
```

Once inside the container, please run:

```
#install chef dk 
apt-get update 
```



## Bootstraping your node 

Any computer that's managed by a Chef server is called a node, and that chef-client is the command that applies the policy defined by your cookbooks to a node. We call the process that installs chef-client on a node and the initial check-in to the Chef server bootstrapping the node.

In this part you'll bootstrap your node and execute the learn_chef_apache2 cookbook on it.
*knife bootstrap* is the command you use to bootstrap a node. As part of the knife bootstrap command, you specify arguments depending on how you would normally connect to your node over SSH.

Go to your Chef Workstation session and run:

~/learn-chef# knife bootstrap 172.17.0.2 --ssh-user root --sudo --identity-file ~/.ssh/id_rsa --node-name node1-ubuntu --run-list 'recipe[learn_chef_apache2]' 

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
IP:          172.17.0.2 
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

