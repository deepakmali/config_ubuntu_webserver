# Steps for configuring the web server
### Get your server.
* Signup and create a server in amazon lightsail.[More Info](https://amazonlightsail.com/docs/)
* add a public key in authorised key
* `ssh-keygen` on local machine
* copy the public key 
* paste it in server in `authorized_key` file under `~/.ssh/` directory in a new line.

### Secure your server.
	sudo apt-get update.
	sudo apt-get upgrade
	Add port 3823 in networking tab of lightsail server dashboard
	Edit /etc/ssh/sshd_config to add the new ssh port 3823	
	restart ssh service `sudo service ssh restart`

### configuring the ufw
	sudo ufw default deny incoming
	sudo ufw default allow outgoing
	sudo ufw allow 3823/tcp
	sudo ufw allow www
	sudo ufw allow ntp
	sudo ufw enable

### Give grader access.
* `sudo apt-get install finger` (optional)
* `sudo adduser grader`
if there is an warning, "perl: warning: Setting locale failed."
set LC_ALL variable to 'C' and add `export LC_ALL='C` it to ~/.bashrc file.
* Enter the relevant details of the user when prompted.
* verify the user is created by `sudo adduser grader`
* Add user to sudoers file
* `sudo cp /etc/sudoers.d/90-cloud-init-users /etc/sudoers.d/grader`
* Edit to change user ubuntu to grader
* Add public keys to authorised keys to enable ssh connection to grader without password.
* use the above setps to generate and add the keys in authorized_keys file.```

### Installing Git and clone the project.
	sudo apt-get install git
	in home directory,
	git clone https://github.com/deepakmali/P5_catalog.git

### Install apache2 and wsgi
* `sudo apt-get install libapache2-mod-wsgi python-dev`
* Enable wsgi `sudo a2enmod wsgi`

### Copy application repo from home folder to /var/www
	cd /var/www
	mkdir Catalog
	cd Catalog
	sudo cp -r ~/P5_catalog .
	sudo mv P5_catalog Catalog

### Install and setup postgres DB
	sudo su - postgres
	postgres@ip-172-26-12-94:~$ psql
	psql (9.5.9)
	Type "help" for help.
	postgres=# CREATE DATABASE CATALOG ;
	CREATE DATABASE
	postgres=# create user appsys with password 'appsys';
	CREATE ROLE
	postgres=# grant all privileges on database catalog to appsys;
	GRANT
	postgres=# \q
	postgres@ip-172-26-12-94:~$ exit
	sudo adduser appsys

### Install flask, sqlalchemy, psycopg2
* `sudo apt-get install python-pip python-flask python-sqlalchemy python-psycopg2`

### Install required python packages.
* `sudo pip install oauth2client requests httplib2`

### Deploy the Item Catalog project.
* Add client_secrets.json and fb_client_secrets.json
* create the wsgi file `sudo vi /var/www/Catalog/catalog.wsgi`
* Paste the below contenets:


    import sys
    import logging
    logging.basicConfig(stream=sys.stderr)
    sys.path.insert(0, '/var/www/Catalog/Catalog')
    from application import app as application
    application.secret_key='super_secret_key'
    
* Create the `/etc/apach2/sites-available/catalog.conf` file
* Paste the below contents:



	<VirtualHost *:80>
	     ServerName <IP Address> 
	     ServerAdmin <Email Address> 
	     #Location of the items-catalog WSGI file
	     WSGIScriptAlias / /var/www/Catalog/catalog.wsgi
	     #Allow Apache to serve the WSGI app from our catalog directory
	     <Directory /var/www/Catalog/Catalog>
	          Order allow,deny
	          Allow from all
	     </Directory>
	     #Allow Apache to deploy static content
	     Alias /static /var/www/Catalog/Catalog/static
	     <Directory /var/www/Catalog/Catalog/static>
	        Order allow,deny
	        Allow from all
	     </Directory>
	      ErrorLog ${APACHE_LOG_DIR}/error.log
	      LogLevel warn
	      CustomLog ${APACHE_LOG_DIR}/access.log combined
	</VirtualHost>


# How to run the application
* Reload the apache `sudo service apache2 reload`
* Open the any web browser and navigate to `http://<ip address>`

# Accessing my hosted application
* Open any web browser
* [Click Here](http://52.66.109.71/) to visit.
* To launch ssh connection use `ssh grader@52.66.109.71 -p 3823`
* To request password or keys [click here](mailto:deepak3823@gmail.com)


# Credits
* [Digital Ocean](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps)
* [Udacity discussion forum](https://discussions.udacity.com)
* [Amazon Lightsail](https://www.google.co.in/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwj71a6GutHWAhXFOo8KHQLkDPQQFggnMAA&url=https%3A%2F%2Famazonlightsail.com%2F&usg=AOvVaw0R-xQizQ5hK-4YkURuGQkU)
* Udacity mentors

