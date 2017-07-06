# Lab 7

PQ: Please complete the previous labs before trying this one.

---

## Chef Supermarket
supermarket.chef.io is the online resource for Chef cookbooks. Anybody can upload cookbooks to it.  

There are several ways to obtain cookbooks from Chef Supermarket. One way is to use the knife supermarket command. However, the chef-client cookbook has dependencies on other cookbooks (you'll learn more about cookbook dependencies in a later module), and knife supermarket does not resolve these dependencies for you.
Berkshelf is a tool that helps you resolve cookbook dependencies. Berkshelf can retrieve the cookbooks that your cookbook depends on and can upload your cookbooks to your Chef server. Berkshelf comes with the Chef DK

.
