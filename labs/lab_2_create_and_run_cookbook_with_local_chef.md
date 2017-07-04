# Lab 2 Create and run a Chef Cookbook with  local Chef #


---

KEY POINT: A cookbook provides structure to your recipes and enables you to more easily reference external files, such as our web server's home page. In essence, a cookbook helps you stay organized. 

---

First start your Docker container 

```
sudo docker run -ti -p 8080:80 --name chef_wk1 chef_wk:1 bash 
```


Now run the following chef generate cookbook command to generate a cookbook named learn_chef_apache2. 

chef generate cookbook cookbooks/learn_chef_apache2 


Create a template 

Now we'll move the home page to an external file. First, run this command to generate the HTML file for our home page. 

chef generate template cookbooks/learn_chef_apache2 index.html 

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

vim learn_chef_apache2/recipes/default.rb 

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



To run the default recipe: 

chef-client --local-mode --runlist 'recipe[learn_chef_apache2]' 

 
Previously, you ran chef-client to run a single recipe from the command line. A run-list specifies each of the individual recipes from your cookbook that you want to apply. Here, you applied just one recipe, but the run-list can contain multiple recipes from multiple cookbooks. 
