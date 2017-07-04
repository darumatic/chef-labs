# Lab 2 Create and run a Chef Cookbook with  local Chef #


---

KEY POINT: A cookbook provides structure to your recipes and enables you to more easily reference external files, such as our web server's home page. In essence, a cookbook helps you stay organized. 

---

First start your Docker container 

```
sudo docker run -ti -p 8080:80 --name chef_wk1 chef_wk:1 bash 
```


Now run the following chef generate cookbook command to generate a cookbook named learn_chef_apache2. 

```
#go to the home directory
cd /root/chef_repo
mkdir cookbooks
cd cookbooks

#generate the cookbook
chef generate cookbook learn_chef_apache2 
```


## Create a template 

A cookbook template is an Embedded Ruby (ERB) template that is used to dynamically generate static text files. Templates may contain Ruby expressions and statements, and are a great way to manage configuration files. 

Now we'll move the home page to an external template file. First, run this command to generate the HTML file for our home page. 

```
chef generate template learn_chef_apache2 index.html 
```

To verify you have your new index.html file inside the templates directory, run the **tree** command as below. 

```
root@772e8db4058e:~/chef-repo/cookbooks# tree  
. 
`-- learn_chef_apache2 
    |-- Berksfile 
    |-- README.md 
    |-- chefignore 
    |-- metadata.rb 
    |-- recipes 
    |   `-- default.rb 
    |-- spec 
    |   |-- spec_helper.rb 
    |   `-- unit 
    |       `-- recipes 
    |           `-- default_spec.rb 
    |-- templates 
    |   |-- default 
    |   `-- index.html.erb 
    `-- test 
        `-- recipes 
            `-- default_test.rb 
```            

Then please modify the content of your template to this (or other content)

```
vim learn_chef_apache2/templates/index.html.erb

<html>
<body>
Hello Chef from a Template inside a Cookbook
<hr>
Node: <%= node[:fqdn] %>
</body>
</html>
```

## Create a recipe inside our cookbook

Our cookbook comes with a default recipe. Let's modify it to get the same web-server as in the previous lab exercise. The recipe is located in /root/chef-repo/cookbooks/learn_chef_apache2/recipes/default.rb Please modify it with the following changes: 

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

template '/var/www/html/index.html' do 
  source 'index.html.erb' 
end 
```

Now, to apply the recipe, please run:

```
chef-client --local-mode --runlist 'recipe[learn_chef_apache2]' 
```

In order to verify everything went fine, you can run 

```
curl localhost
```

You should get the template's content. Additionally you can verify you have the same content from a browser inside your host you can go to: **http://VM_IP:8080**


What is the runlist argument?
 
Previously, you ran chef-client to run a single recipe from the command line. A run-list specifies each of the individual recipes from your cookbook that you want to apply. In the command abobe you applied just one recipe, but the run-list can contain multiple recipes from multiple cookbooks. 

## Closing and saving your container

In order to close and save your container, type Ctrl+D and then type in your host:

```
sudo docker commit chef_wk1 chef_wk:2 
```

