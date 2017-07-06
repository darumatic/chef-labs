# Lab 7

Pre-requisites: Please complete the previous labs before trying this one.

---

## Chef Supermarket
supermarket.chef.io is the online resource for Chef cookbooks. Anybody can upload cookbooks to it.  

There are several ways to obtain cookbooks from Chef Supermarket. One way is to use the ```knife supermarket``` command. However, the chef-client cookbook has dependencies on other cookbooks and knife supermarket does not resolve these dependencies for you.

Berkshelf is a tool that helps you resolve cookbook dependencies. Berkshelf can retrieve the cookbooks that your cookbook depends on and can upload your cookbooks to your Chef server. Berkshelf comes with the Chef DK


## Install htop cookbook

First, you need to setup Bershelf to specify which cookbooks you want. In /root/chef-repo directory create ```Berksfile``` with this content:

```
source 'https://supermarket.chef.io'
cookbook 'htop'
```

Now run ```berks install``` to download the *htop* cookbook and its dependencies.

After downloading the new cookbook you need to upload it to the server. In this case you can't use *knife* since you want to upload all the dependencies in one go. 

```
berks upload --no-ssl-verify
```

We specify *--no-ssl-verify* since our Chef-server certificate is self signed and not approved by any authority. 

## Roles
To deploy the new cookbook we will use a role. Roles enable to define the functionality that a node performs collectively rather than specifying individual components (as in the run-list) Fo example a web server role, a database role or a load balancer role.

Roles are stored as objects on the Chef Server. You can specify the roles in a json file.

First create the roles directory:
```
mkdir /root/chef-repo/roles
```

The create the roles file in /root/chef-repo/roles/web.json

```
{
   "name": "web",
   "description": "Web server role.",
   "json_class": "Chef::Role",
   "override_attributes": {
   },
   "chef_type": "role",
   "run_list": ["recipe[htop::default]",
                "recipe[learn_chef_apache2::default]"
   ],
   "env_run_lists": {
   }
}
```

Then, we upload the role to the Chef Server
```
knife role from file roles/web.json
```

And to verify the role is present we can run: ```knife role list``` We should get ```web``` as the result.

Alternatively, the ```knife role show web``` also display role information from the server. 


Finally, we link our node with the new role:
```
knife node run_list set node1-ubuntu "role[web]"
```


## Executing the role

To verify our node's functionality:

```
knife node show node1-ubuntu --run-list
```

We should get the following, which shows our web role linked to the ubuntu node: 
```
node1-ubuntu:
  run_list: role[web]
```

Finally, we use the knife ssh command to run the chef-client in our node.

knife ssh 'role:web' 'sudo chef-client' --ssh-user root --identity-file ~/.ssh/id_rsa --attribute ipaddress

Please notice that we don't specify the ip of the node. Just the role we want to apply.

To verify the *htop* cookbook, we can run the htop command from the Chef Node.

```
htop
```




