# Lab 6 Modify your cookbook

---
Pre-requisites: As usual you need to have the previous labs finished.
---


# Metadata file

Inside the cookbook directory we have a file named metadata.rb. The contents of the metadata.rb file provides hints to the Chef server to help ensure that cookbooks are deployed to each node correctly.

A metadata.rb file have the following characteristics:

* Located at the top level of a cookbook’s directory structure.
* Compiled whenever a cookbook is uploaded to the Chef server or when the knife cookbook metadata subcommand is run, and then stored as JSON data.
* Created automatically by knife whenever the knife cookbook create subcommand is run.
* Can be edited by the user, and then re-uploaded to the Chef server as part of a cookbook upload.


# Semantic Versioning

According to the proposal in semver.org versions should have the format major.minor.patch

* Major specifies a breaking change, not compatible with the previous one. 
* Minor is for backwards compatible changes
* Patch is for bug fixes.

For clarity, it's better to use this proposal within the metadata.rb file.


# Modify your cookbook and upload a new version

1. Update your template in /root/chef-repo/cookbooks/learn_chef_apache2/templates/index.html.erb to look like this: 
```
<html>
<body>
Hello Chef from a Template inside a Cookbook
<br>
New and improved (minor) version.
<hr>
Node FQDN: <%= node[:fqdn] %>
</body>
</html>
```
2. Increate the metadata.rb version to 0.2.0 The file located in /root/chef-repo/cookbooks/learn_chef_apache2/metadata.rb 
3. Upload the cookbook using the command:
```
knife cookbook upload learn_chef_apache2
```
4. Run the new cookbook in your node:
```
knife ssh 'name:node1-ubuntu' 'sudo chef-client' --ssh-user root --identity-file ~/.ssh/id_rsa --attribute ipaddress
```
5. Verifying the result. In order to see the updated web page go to the following address: http://localhost:8082

# Discussion

1. What happens when you run ```chef-client``` from the node? Help: Run the command with |less at the end (as in ```chef-client|less```) for more information.  

2. How would you execute the chef-client on your nodes?

