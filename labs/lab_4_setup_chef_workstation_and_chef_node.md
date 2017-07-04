# Lab 4 Setup Chef Workstation and Chef node

Pre-requisites:

You need to have the Chef Server from Lab 3 working and the Docker image from lab 2 also saved in your local system.

---

From the host, you need to get the ip of the Chef Server machine. In order to do it run the following command:
```
sudo docker inspect chef-server |grep IPAdd
```
Take note of the ip address, you will use it later. 


Run your Chef Workstation

```
sudo docker run -ti --name chef_wk2 -h chef_wk chef_wk:2 bash 
```

Now modify the /etc/hosts file and add the Chef Server ip. This will enable to use the name of the host instead of the ip in the following commands. Assuming the ip is 172.17.0.2, the line you need to add looks like this:

```
172.17.0.2 chef-server
```

You can verify the host is working by running this command:
```
ping chef-server
```

##Installing *knife* credentials

Now that we have connectivity from the chef workstation to the chef server, we will install the *knife* credentials:

```
cd
mkdir -p learn-chef/.chef
cd learn-chef/.chef
curl -Ok https://chef-server:443/knife_admin_key.tar.gz  
tar xvzf knife_admin_key.tar.gz 
```

Your /root/learn-chef/.chef/knife.rb should look like this:
```
current_dir = File.dirname(__FILE__) 
log_level                 :info 
log_location              STDOUT 
cache_type               'BasicFile' 
node_name                 "admin" 
client_key                "#{current_dir}/admin.pem" 
validation_client_name   "my_org-validator" 
validation_key           '#{current_dir}/my_org-validator.pem' 
chef_server_url           "https://chef-server/organizations/my_org" 
cookbook_path             ["#{current_dir}/../cookbooks"] 
```

#When the config.rb file is ready, you will need to get the SSL certificate file from the container to access Chef Server:

```
knife ssl fetch 
```

This is the expected output:
```
WARNING: Certificates from 44ad2386dc74 will be fetched and placed in your trusted_cert 
directory (/root/learn-chef/.chef/trusted_certs). 

Knife has no means to verify these are the correct certificates. You should 
verify the authenticity of these certificates after downloading. 

Adding certificate for chef-server in /root/learn-chef/.chef/trusted_certs/chef-server.crt 
```

Then to verify ssl connectivity:

```
knife ssl check 
```

Should return:

```
Connecting to host chef-server:443 
Successfully verified certificates from `chef-server' 
```

Finally to verify we can connect to the Chef Server:

```
root@44ad2386dc74:~/learn-chef/.chef# knife user list 
```

Should return: ```admin``` 


At this point, you have two of the three parts of a typical Chef setup. Your workstation is set up and you have access to the Chef server.

Chef server gives you a persistent location to store your cookbooks and information about your nodes. The knife command enables you to communicate with the Chef server.

