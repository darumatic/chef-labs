# Lab 1 Create a resource with local Chef

---
KEY POINTS:
* A Chef resource describes one part of the system, such as a file, a template, or a package.

* A Chef recipe is a file that groups related resources, such as everything needed to configure a web server, database server, or a load balancer.
---


```　
#create env 
docker run -ti --name chef_wk ubuntu:14.04 bash 

Once inside the container, please run:

#install chef dk 
apt-get update 
apt-get -y install curl vim nano
curl https://omnitruck.chef.io/install.sh | bash -s -- -P chefdk -c stable -v 0.18.30 
```

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
  content 'hello world' 
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

