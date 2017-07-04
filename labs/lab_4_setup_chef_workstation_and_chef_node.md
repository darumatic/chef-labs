# Lab 4 Setup Chef Workstation and Chef node

Pre-requisites:

You need to have the Chef Server from Lab 3 working and the Docker image from lab 2 also saved in your local system.

---

Run your Chef Workstation

```
sudo docker run -ti --name chef_wk2 -h chef_wk chef_wk:2 bash 
```

```
cd
mkdir learn-chef/.chef
cd learn-chef/.chef
curl -Ok https://localhost:443/knife_admin_key.tar.gz  
tar xvzf knife_admin_key.tar.gz 

#your knife.rb should look like this:
root@44ad2386dc74:~/learn-chef/.chef# cat ~/learn-chef/.chef/knife.rb 
current_dir = File.dirname(__FILE__) 
log_level                 :info 
log_location              STDOUT 
cache_type               'BasicFile' 
node_name                 "admin" 
client_key                "#{current_dir}/admin.pem" 
validation_client_name   "my_org-validator" 
validation_key           '#{current_dir}/my_org-validator.pem' 
chef_server_url           "https://localhost/organizations/my_org" 
cookbook_path             ["#{current_dir}/../cookbooks"] 

```

#When the config.rb file is ready, you will need to get the SSL certificate file from the container to access Chef Server:
knife ssl fetch 
WARNING: Certificates from 44ad2386dc74 will be fetched and placed in your trusted_cert 
directory (/root/learn-chef/.chef/trusted_certs). 

Knife has no means to verify these are the correct certificates. You should 
verify the authenticity of these certificates after downloading. 

Adding certificate for chef-server in /root/learn-chef/.chef/trusted_certs/chef-server.crt 

root@44ad2386dc74:~/learn-chef/.chef# knife ssl check 
Connecting to host chef-server:443 
Successfully verified certificates from `chef-server' 

root@44ad2386dc74:~/learn-chef/.chef# knife user list 
admin 

At this point, you have two of the three parts of a typical Chef setup. Your workstation is set up and you have access to the Chef server.
Chef server gives you a persistent location to store your cookbooks and information about your nodes. The knife command enables you to communicate with the Chef server.

