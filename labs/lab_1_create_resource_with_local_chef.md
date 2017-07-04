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

```
Why nothing changed?

Chef looks at the current configuration state and applies the action only if the current state doesn't match the desired state. Here, Chef doesn't create or modify /tmp/motd because it already exists and its contents didn't change. We call this approach *test and repair*. 

---

## Extra ##

1. Modify the content of /tmp/motd
2. Run the chef-cliet command as above. What happened with the content of /tmp/motd? Why? 



