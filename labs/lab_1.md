# Lab 1 Create a resource with local Chef

---
KEY POINTS:
* A Chef resource describes one part of the system, such as a file, a template, or a package.

* A Chef recipe is a file that groups related resources, such as everything needed to configure a web server, database server, or a load balancer.
---

## Setup your Docker environment and install Chef-DK

We will start by creating a Docker 'light virtual machine' running in our host. This approach enables us to not modify the host environment and to experiment as much as we want with our Chef configuration without making any external modification. At any point we can just start creating a new Docker container.

This command starts a Docker container named *chef_wk* and based on Ubuntu 14.04. It will also export the http port so we can access the Apache web server from the host.

```　
#create env 
sudo docker run -ti -p 8080:80 --name chef_wk ubuntu:14.04 bash 
```



Once inside the container, please run:

```
#install chef dk 
apt-get update 
apt-get -y install curl vim nano tree git openssh-client
curl https://omnitruck.chef.io/install.sh | bash -s -- -P chefdk -c stable -v 0.18.30 
```

## Create a file with chef-client

By this point you have the chef development kit installed. Let's try it out:

Please type:
```
cd 
mkdir /root/chef-repo 
cd chef-repo/ 
```

Now with vim or nano, create a file called /root/chef-repo/hello.rb with the following content:

```
file '/tmp/motd' do 
  content 'hello world
  ' 
end 
```
Finally, run the following command:

```
chef-client  --local-mode hello.rb 
```

The command above instructed chef to create a message of the day file in the /tmp directory. To verify it:

```
root@df67a8dfd2d7:~/chef-repo# cat /tmp/motd 
hello world
```

---

Let's run again the same command:

```
chef-client  --local-mode hello.rb 
```


Why nothing changed?

Chef looks at the current configuration state and applies the action only if the current state doesn't match the desired state. Here, Chef doesn't create or modify /tmp/motd because it already exists and its contents didn't change. We call this approach **test and repair**. 

---

## Change the managed file 

1. Modify the content of /tmp/motd
2. Run the chef-cliet command as above. What happened with the content of /tmp/motd? Why? 

## Configure a package and a service

The recibe below will perform a few action:
1. Update the apt cache daily
2. Install the apache web server
3. Set up a hello wold file for our web server.

To achieve this, you can create /root/chef-repo/web.rb with the following content:

```
apt_update 'Update the apt cache daily' do 
  frequency 86_400 
  action :periodic 
end 

package 'apache2' 

service 'apache2' do 
  supports :status => true 
  action [:enable, :start] 
end 

file '/var/www/html/index.html' do 
  content '<html> 
  <body> 
    <h1>hello Chef World</h1> 
  </body> 
</html>' 
end 

```

Now execute: 

```
chef-client  --local-mode web.rb 
```

To verify everything is working correctly, you can run

```
curl http://localhost 
```

Your output should be: 

```
<html> 
  <body> 
    <h1>hello world</h1> 
  </body> 
</html>
```

From your host machine you can go to http://VM_IP:8080 to get the Apache web page.


## Closing and saving your container

In order to close and save your container, type Ctrl+D and then type in your host:

```
sudo docker commit chef_wk chef_wk:1 
```


