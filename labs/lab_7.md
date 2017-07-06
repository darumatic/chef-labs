# Lab 7

PQ: Please complete the previous labs before trying this one.

---

## Chef Supermarket
supermarket.chef.io is the online resource for Chef cookbooks. Anybody can upload cookbooks to it.  

There are several ways to obtain cookbooks from Chef Supermarket. One way is to use the ```knife supermarket``` command. However, the chef-client cookbook has dependencies on other cookbooks and knife supermarket does not resolve these dependencies for you.

Berkshelf is a tool that helps you resolve cookbook dependencies. Berkshelf can retrieve the cookbooks that your cookbook depends on and can upload your cookbooks to your Chef server. Berkshelf comes with the Chef DK


## Install motd cookbook

First, you need to setup Bershelf to specify which cookbooks you want. In /root/chef-repo directory create ```Berksfile``` with this content:

```
source 'https://supermarket.chef.io'
cookbook 'motd'
```

Now run ```berks install``` to download the *motd* cookbook and its dependencies.

After downloading the new cookbook you need to upload it to the server. In this case you can't use *knife* since you want to upload all the dependencies in one go. 

```
berks upload --no-ssl-verify
```

We specify *--no-ssl-verify* since our Chef-server certificate is self signed and not approved by any authority. 

