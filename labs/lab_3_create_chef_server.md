
# Create Chef Server

```
sudo docker run --privileged -ti -p 443:443 -p 8000:8000 -p 80:8080 -h cserver --name cserver ubuntu:14.04 bash 	
```

Once your container starts, please run these commands:

```
#update the system 
apt-get update -y 
#install some packages
apt-get install -y wget vim curl git ntp nano tree
#download chef server
wget --no-check-certificate --content-disposition "http://www.opscode.com/chef/download-server?p=ubuntu&pv=14.04&m=x86_64&v=12&prerelease=false&nightlies=false" 
```

Once we have our system ready, we will insall Chef Server

```
#we need to increase the shared memory segment to get Chef Server working
sysctl -w kernel.shmmax=17179869184
#now we install the Chef Server package
dpkg -i chef-server-core*.deb 
/opt/opscode/embedded/bin/runsvdir-start &
echo "nginx['enable_non_ssl']=false" > /etc/opscode/chef-server.rb
echo "nginx['ssl_port']=443" >> /etc/opscode/chef-server.rb
echo "nginx['server_name']=\"chef-server\"" >> /etc/opscode/chef-server.rb
```

```
#to start all the services
chef-server-ctl reconfigure
```

```
#to verify the installation went well, you need to run
curl http://127.0.0.1:8000/_status
You should get something like:
root@d238720ab44d:~# curl http://127.0.0.1:8000/_status 
{"status":"pong","upstreams":{"chef_solr":"pong","chef_sql":"pong","oc_chef_authz":"pong"},"keygen":{"keys":10,"max":10,"max_workers":4,"cur_max_workers":4,"inflight":0,"avail_workers":4,"start_size":0},"analytics_queue":{"queue_at_capacity":false,"dropped_since_last_check":0,"max_length":10000,"last_recorded_length":0,"total_dropped":0,"check_count":9,"mailbox_length":0}}root@d238720ab44d:~# 

#(for troubleshooting)
#chef-server-ctl cleanse
#chef-server-ctl reconfigure
```

```
echo -e "\nSetting up admin user and default organization" 
chef-server-ctl user-create admin Admin User admin@myorg.com "passwd"  --filename /etc/chef/admin.pem 
chef-server-ctl org-create my_org "Default organization" --association_user admin --filename /etc/chef/my_org-validator.pem 
```

```
echo -e "\nRunning: chef-server-ctl install chef-manage" 
chef-server-ctl install chef-manage 

echo -e "\nRunning: chef-server-ctl reconfigure" 
chef-server-ctl reconfigure 

echo "error Please use https instead of http" > /var/opt/opscode/nginx/html/500.json

sed -i "s,/503.json;,/503.json;\n    error_page 497 =503 /500.json;,g" /var/opt/opscode/nginx/etc/chef_https_lb.conf 

sed -i '$i\    location /knife_admin_key.tar.gz {\n      default_type application/zip;\n      alias /etc/chef/knife_admin_key.tar.gz;\n    }' /var/opt/opscode/nginx/etc/chef_https_lb.conf 
```

```
echo -e "\nCreating tar file with the Knife keys" 
cd /etc/chef/ && tar -cvzf knife_admin_key.tar.gz admin.pem my_org-validator.pem 
```

```
echo -e "\nRestart Nginx..." 
chef-server-ctl restart nginx 
chef-server-ctl status 
touch /root/chef_configured 
```


- When you create your user account, note your username, password, and location of your RSA private key.
- When you create your organization, note the name of the organization you choose.


# Troubleshooting:
In case the apt-get update or other networking related command doesn't work due to not connectivity you can try:

1. Exit the container
2. ```sudo service docker restart```
3. Start the container again, typically ```sudo docker start -i container_name   ```
