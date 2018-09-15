# Linux Server Configuration

## Project Overview

>The project's goal was to do a baseline installation of a Linux server and to prepare it to host a web application. This contains to secure the server from a number of attack vectors, to install and configure a database server and to deploy one of my existing web applications onto it.

## Project Result
>The result of the project can be visited under [http://18.185.89.147](http://18.185.89.147) or [http://ec2-18-185-89-147.eu-central-1.compute.amazonaws.com/](http://ec2-18-185-89-147.eu-central-1.compute.amazonaws.com/) (as long as my Amazon Lightsail instance is active).

## General conditions
These are the general conditions for this project:

- Use a server instance from [Amazon Lightsail](https://aws.amazon.com/de/lightsail/?sc_channel=PS&sc_campaign=acquisition_DEsc_publisher=google&sc_medium=ACQ-P%7CPS-GO%7CBrand%7CDesktop%7CSU%7CCompute%7CLightsail%7CDE%7CEN%7CText&sc_content=lightsail_e&sc_detail=amazon%20lightsail&sc_category=Compute&sc_segment=293644508117&sc_matchtype=e&sc_country=DE&s_kwcid=AL!4422!3!293644508117!e!!g!!amazon%20lightsail&ef_id=W5lNNwAAAImYcDaw:20180912173031:s)

- Use Linux distribution [Ubuntu 16.04](http://releases.ubuntu.com/16.04/)

- The application which is to be deployed is the [Item Catalog project](https://github.com/DomiWest/item-catalog_project) created in Udacity's [Fullstack Webdeveloper Nanodegree program](https://eu.udacity.com/course/full-stack-web-developer-nanodegree--nd004).

- The database server is [PostgreSQL](https://www.postgresql.org/).

## References
- [boisalai/udacity-linux-server-configuration](https://github.com/boisalai/udacity-linux-server-configuration)

- [kongling893/Linux-Server-Configuration-UDACITY](https://github.com/kongling893/Linux-Server-Configuration-UDACITY)

- [How To Deploy a Flask Application on an Ubuntu VPS - Digital Ocean](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps)

- [Deploy a Flask Application on Ubuntu 14.04 - ProfitBricks](https://devops.profitbricks.com/tutorials/deploy-a-flask-application-on-ubuntu-1404/)  

##Steps to complete the project  
  
###A. Get a server  
###1. Start a new Ubuntu Linux server instance on [Amazon Lightsail](https://aws.amazon.com/de/lightsail/?sc_channel=PS&sc_campaign=acquisition_DEsc_publisher=google&sc_medium=ACQ-P%7CPS-GO%7CBrand%7CDesktop%7CSU%7CCompute%7CLightsail%7CDE%7CEN%7CText&sc_content=lightsail_e&sc_detail=amazon%20lightsail&sc_category=Compute&sc_segment=293644508117&sc_matchtype=e&sc_country=DE&s_kwcid=AL!4422!3!293644508117!e!!g!!amazon%20lightsail&ef_id=W5lNNwAAAImYcDaw:20180912173031:s)

- Create an account for Amazon Web Services and log in to Amazon Lightsail

- When logged in click `create instance`

- Select platform `Linux/Unix` with blueprint `OS Only` and `Ubuntu 16.04 LTS`

- Choose an `instance plan` that fits your needs

- Name your instance and click `Create`

- After the instance was successfully created and shows status "running" click on it for further information and the possibility to connect   

- Click `Connect using SSH` to connect to the instance as user `ubuntu`

###B. Secure the server  
###2. Update all currently installed packages    

To update installed packages run:

```
sudo apt-get update  
sudo apt-get upgrade
```

###3. Change the SSH port from 22 to 2200  

- Run command `sudo nano /etc/ssh/sshd_config` to edit the `sshd_config-file`.  

- The first lines of the file look like this:
  ```
  #Package generated configuration file  
  # See the sshd_config(5) manpage for details  

  # What ports, IPs and protocols we listen for  
  Port 22
  ```

- Change line 5 which states `Port 22` to `Port 2200`, save and exit the editor.  

- Afterwards the file looks like this:
  ```
  #Package generated configuration file  
  # See the sshd_config(5) manpage for details  

  # What ports, IPs and protocols we listen for  
  Port 2200
  ```

###4. Configure the Uncomplicated Firewall (UFW)

- To configure the UFW run:
  ```
  sudo ufw default deny incoming  
  sudo ufw default allow outgoing      
  sudo ufw allow 2200/tcp  
  sudo ufw allow www  
  sudo ufw allow 123/udp
  sudo ufw deny 22
  ```
- To start the UFW run command: `sudo ufw enable`

###5. Configure the Lightsail Firewall

- Return to the Lightsail webpage and change to the `Networking-Tag`

- Change the firewall configuration to:
  ```
  | Application | Protocol | Port range |  
  |-------------|----------|------------|  
  | HTTP        | TCP      | 80         |  
  | Custom      | UDP      | 123        |  
  | Custom      | TCP      | 2200       |  
  ```

###C. Give `grader` access  
###6. Create a new user named `grader`  

- Run command `sudo adduser grader`, enter a password and user-details (optionally).

###7. Give `grader` the permission to `sudo`

- Run command `sudo visudo` to edit the sudoers-file.

- Search for line
  ```
  root    ALL=(ALL:ALL) ALL
  ```

- Add another line underneath
  ```
  root    ALL=(ALL:ALL) ALL
  grader  ALL=(ALL:ALL) ALL        
  ```

###8. Create an SSH key pair for `grader`

- Download PuTTY and follow the instructions in this [Youtube-Video](https://www.youtube.com/watch?v=bi7ow5NGC-U) from LinuxAcademy.com

- In order to follow these instruction a private key has to be dowloaded from the 'Account' menu on Amazon Lightsail. To do so click on `SSH keys` tab and download the `Default Private Key`.

###D. Prepare to deploy the project
###9. Configure the local timezone to UTC
- The local timezone is already set to UTC by default.  

- It could have been changed by running the command `sudo dpkg-reconfigure tzdata` and following the prompt.

###10. Install and configure Apache to serve a Python mod_wsgi application
While logged in as `grader`:  

- Run command `sudo apt-get install apache2` to install Apache

- Run command `apt-get download libapache2-mod-wsgi` to install `mod_wsgi`

- Run command `sudo a2enmod wsgi` to enable mod_wsgi

###11. Install and configure PostgreSQL  
While logged in as `grader`:  

- Run command `sudo apt-get install postgresql` to install `PostgreSQL`

- Run command `sudo su - postgres` to switch to the postgres-user

- Run command `psql` to open psql

- To create a user called `catalog` and to give it the ability to create databases:
  ```'
  postgres=# CREATE ROLE catalog WITH LOGIN PASSWORD 'catalog';  
  postgres=# ALTER ROLE catalog CREATEDB;
  ```

- Run command `\q` to exit psql

- Run command `exit` to return to user `grader`

- Run command `sudo adduser catalog` to create a new user on Linux called `catalog`

- Run command `sudo visudo` to edit the sudoers-file and to make `catalog` a sudo-user

- Search for lines
  ```
  root    ALL=(ALL:ALL) ALL
  grader  ALL=(ALL:ALL) ALL
  ```

- Add another line underneath
  ```
  root    ALL=(ALL:ALL) ALL
  grader  ALL=(ALL:ALL) ALL     
  catalog ALL=(ALL:ALL) ALL
  ```

- Run command `su - catalog` to login as `catalog`

- Run command `createdb catalog` to create a database named catalog

- Run command `exit` to return to user `grader`

###12. Install `git`

- Run command `sudo apt-get install git` to install `git`

###E. Deploy the Item Catalog project
###13. Clone and setup Item Catalog project from Github

- Run command `sudo mkdir /var/www/catalog` to create directory `/var/www/catalog`

- Run command `cd /var/www` to change to directory `/var/www`

- Run command `sudo chown -R grader:grader catalog/` to change the ownership of the directory `catalog` to user `grader`

- Run command `cd /var/www/catalog` to change to directory `/var/www/catalog`

- Run command `sudo git clone https://github.com/DomiWest/item-catalog_project` to clone the files from the item catalog project on Github to this directory

- Run command `sudo mv item-catalog_project catalog` to change the cloned directory's name from `item-catalog_project` to `catalog`

- Run command `cd catalog` to change to directoy `/var/www/catalog/catalog`

- Run command `mv project.py __init__.py` to rename file `project.py`

- Run command `sudo nano __init__.py` to edit the file

- Modify the end of the file like this:
  ```
  # app.run(host="0.0.0.0", port=5000)
  app.run()
  ```

- In files `__init__.py`, `lotsofmeals.py` and `database_setup.py` change from using SQLite to using PostgreSQL:
  ```
  # engine = create_engine("sqlite:///mealingredients.db")
  engine = create_engine('postgresql://catalog:catalog@localhost/catalog')
  ```

Go to [GoogleAPI Dashboard](https://console.developers.google.com/apis/dashboard?project=neighborhood-map-210519&duration=P30D) and change the `credentials` of the `item catalog project`:

- Add `http://18.185.89.147` and `http://ec2-18-185-89-147.eu-central-1.compute.amazonaws.com/` as authorized JavaScript origins

- Add `http://ec2-18-185-89-147.eu-central-1.compute.amazonaws.com/login` as authorized Redirect URI

- Download the JSON file and copy the contents

- Paste the content to the `client_secrets.json` file on the linux machine

- Run command `sudo nano client_secrets.json` to edit the file

###14. Set up the application correctly

- Run command `sudo apt-get install python-pip` to install pip

- Run command `sudo apt-get install python-virtualenv` to install virtual environment

- Run command `sudo virtualenv -p python venv2` to create the virtual environment

- Run command `sudo chown -R grader:grader venv2/` to change the ownership of the virtual environment to `grader`

- Run `. venv2/bin/activate` to activate the virtual environment

- Run the following commands to install important dependencies:
  ```
  sudo pip install httplib2
  sudo pip install requests
  sudo pip install --upgrade oauth2client
  sudo pip install sqlalchemy
  sudo pip install flask
  sudo sudo apt-get install libpq-dev
  sudo pip install psycopg2
  ```

- Run command `python __init__.py` to start the item catalog application

- Run command `deactivate` to deactivate the virtual environment

- Run command `sudo nano /etc/apache2/sites-available/catalog.conf` to create a config file and add the following lines:
  ```
    <VirtualHost *:80>
        ServerName 18.185.89.147
        ServerAlias ec2-18-185-89-147.eu-central-1.compute.amazonaws.com
        WSGIScriptAlias / /var/www/catalog/catalog.wsgi
        <Directory /var/www/catalog/catalog/>
            Order allow,deny
            Allow from all
        </Directory>
        Alias /static /var/www/catalog/catalog/static
        <Directory /var/www/catalog/catalog/static/>
            Order allow,deny
            Allow from all
        </Directory>
        ErrorLog ${APACHE_LOG_DIR}/error.log
        LogLevel warn
        CustomLog ${APACHE_LOG_DIR}/access.log combined
    </VirtualHost>
  ```

- Run command `sudo a2ensite catalog` to enable the virtual host

- Run command `sudo service apache2 reload` to reload Apache

- Run command `sudo nano /var/www/catalog/catalog.wsgi` to create a file and add the following lines:

  ```
  activate_this = '/var/www/catalog/catalog/venv2/bin/activate_this.py'
  with open(activate_this) as file_:
      exec(file_.read(), dict(__file__=activate_this))

  #!/usr/bin/python
  import sys
  import logging
  logging.basicConfig(stream=sys.stderr)
  sys.path.insert(0, "/var/www/catalog/catalog/")
  sys.path.insert(1, "/var/www/catalog/")

  from catalog import app as application
  application.secret_key = "CLIENT_ID"
  ```

- Run command `sudo service apache2 restart` to restart Apache

- Run command `sudo a2dissite 000-default.conf` to disable the default Apache site

- Run command `sudo chown -R www-data:www-data catalog/` to change ownership of the project's directory

- Run command `sudo service apache2 restart` to restart Apache

- Go to the Browser to http://ec2-18-185-89-147.eu-central-1.compute.amazonaws.com/ or http://18.185.89.147 and see that the application is running
