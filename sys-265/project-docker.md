# Project: Docker

## Build Steps Taken

* Install docker on the desired machine, in this case I already had it from a previous lab
* Create a directory for the new docker files

```
mkdir odoo cd odoo
```

* Inside that directory create the docker-compose.yml file. The file that I used can be found below:

![Docker-compose ymal file for odoo and the&#x20;
postgress database](https://github.com/user-attachments/assets/3dbb71f8-cc28-4b6c-9b15-1af9a4145ad6)

* Once the compose file is created, make the .env file. The file that I used can be found below:

![The .env file used for passing passwords and users
to the postgress database](https://github.com/user-attachments/assets/09b25f81-9874-482e-b577-ab52b7dcb5dd)

* Once these are both made and checked, use the docker up -d command in this directory
* Next, you have to install nginx, I used the following commands to do so:

```
apt install nginx 
ufw allow "Nginx Full" 
nano /etc/nginx/sites-available/odoo.conf
```

* Inside of this conf file, you want to place all the configurations for the nginx server. The configurations that I used can be found below:

![Nginx configuration file used for the odoo web hosting.](https://github.com/user-attachments/assets/50f8358c-5bd5-4bc3-a60e-e2b9fe41c663)

* Lastly, you want to move the file so that it is public and test the configurations

```
ln -s /etc/nginx/sites-available/odoo.conf /etc/nginx/sites-enabled/ 
nginx -t 
systemctl reload nginx.service
```

* Once all of this is completed, you can finish the install by using the web interface and supplying the necessary information for the database creator.
* Congratulations on your odoo instance through docker success!
