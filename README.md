# Linux-Server-Configuration

I would describe the steps in order to set up and configure a Linux Server, based on an instance on Lightsail Amazon Web Service that would host a Flask app. This was part of the Full Stack Developer Nano Degree course from Udacity and the project was Linux Server Configuration. 


Information about Amazon Lightsail Linux Instance:

Public IP Address: 3.82.189.241 

Port: 2200 

## Amazon Lightsail instance

Create an instance on Amazon Lightsail in Linux platform with Ubuntu 16.04 LTS. Once you get the instance, connected using the Orange button "Connecting Using SSH". When get into the Linux platform is necessary to made some steps configuration described below that will be done with the ubuntu default user. These steps are required in order to upgrade Linux packages and configure the Firewall required for this project: 

* ``` sudo apt-get upgrade ```
* ``` sudo apt-get update ``` 
* ``` sudo nano cd etc/.ssh/sshd_config ``` Add the 2200 port in the etc/ssh/sshd_config file in the line below of port 22(default port)

* On Amazon Ligthsail web site, add a custom port 2200 in the Networking tab 

* ``` sudo ufw default deny incoming ``` (by default deny all incoming connections)

* ``` sudo ufw default allow outgoing ``` (default to deny  outgoing connections)

Configure firewalls rules:

* ```sudo ufw allow ssh```

* ```sudo ufw allow 2200/tcp``` (allow all TCP connections through port 2200)

* ```sudo ufw allow 80/tcp```

* ```sudo ufw allow 123``` 

* ```sudo ufw enable``` (to enable the firewall)


## Create the Grader User 

The following commands will be executed in order to create the grader user and allow ssh key authentication connection for the grader user into the server. The ssh authentication consist of a public and private key that works together. The public key would be copied to the server and the private key is the key that the user store in the local computer and use to connect to the server. The public key would be copied to the /home/grader/.ssh/authorized_keys file in the Linux platform. 

The following commands should be running from the server: 

* ```sudo add grader``` Create the grader user

* ```sudo usermod -a -G sudo grader``` This give sudo permissions to the grader user 

* ```su - grader``` To change to the grader user in the Linux platform

* ```mkdir .ssh``` Create the .ssh dirctory in Linux

* ```touch .ssh/authorized_keys``` Create the file authorized_keys in the .ssh directory

From your local computer run the following command: 

* ```ssh-keygen``` 

This command generate two keys, one public and one private. The public key has to be copied into the authorized_keys file on the Linux instance. The private key should be stored in the local computer and use every time the grader user connect into the server.  

* open the .ssh/authorized_keys and copy the public key that you get with the previous command. Follow this tutorial: https://www.digitalocean.com/community/tutorials/how-to-configure-ssh-key-based-authentication-on-a-linux-server

Change permissions to the .ssh directory and the authorized_keys in the Linux instance:

* ```chmod 700 /home/grader/.ssh```

* ```chmod 644 /home/grader/.ssh/authorized_keys```

Finally, change the owner of the directory and authorized_keys file with:

* ```sudo chown grader .ssh```

* ```service ssh restart```

Now you can use ssh to login with the new user you created running the following command from your local computer:

* ```ssh -i [privateKeyFilename] grader@3.82.189.241``` 


# Application Functionality

This app would be deployed using Flask, Apache2 and Postgresql in Linux remote system using Amazon Lightsail. The following steps consists on the configuration of a database and a web server as well as describe the steps to clone the Flask application into the Linux instance and install all the software required. The WSGI module will be used as an interface between Flask and Apache Server.  

## Install and configure Apache to serve a Python mod_wsgi application
  
* ```sudo apt-get install apache2``` Install Apache 

* ```sudo apt-get install libapache2-mod-wsgi-py3``` Install mod_wsgi

### Install git, clone and setup your Catalog App project
 
* ```sudo apt-get install git``` Install Git 

* ```cd /var/www``` to move to the /var/www directory

* ```sudo mkdir catalogapp``` Create the application directory 

* ```cd catalogapp``` Move into the catalopapp dicrectory 

* ```git clone https://github.com/nicolasferrari/catalogapp.git catalogapp``` Clone the Catalog App to the virtual machine


Move into the /var/www/catalogapp/catalogapp 
 
* ```sudo mv application.py __init__.py``` Rename application.py file to __init__.py 

Change the path of the client_id file for oauth2 authentication and locate into the catalogapp path. With the command: 

* ```sudo nano __init__.py```  change the line CLIENT_ID = json.loads(open('client_secrets.json', 'r').read())['web']['client_id'] to CLIENT_ID = json.loads(open('/var/www/catalogapp/catalogapp/client_secrets.json','r').read())[web']['client_id']

## Install virtual environment

* ```sudo apt-get install python-pip```
* ```sudo pip install virtualenv```
* ```sudo virtualenv venv```
* ```source venv/bin/activate```
* ```sudo chmod -R 777 venv```

You should see a (venv) appears before your username in the command line. 

### Install packages for running the app

*```pip3 install Flask```
*```pip3 install requests```
*```pip3 install httplib2```
*```pip3 install oauthclient```
*```pip3 install sqlalchemy```
*```pip3 install psycopg2```
*```pip3 install sqlalchemy_utils```


## Install and Configure  Postgresql 

* ```sudo apt-get install postgresql```

* ```sudo nano /ect/postgresql/9.5/main/pg_hba.conf``` Check that remote connections are not allowed

* ```sudo -u postgres -i``` to login as postgres default user

* ```psql``` to get to the command line interfase of postgresql

Run the following commands in order to create the catalog user and the mineralsitemsusers database

* ```CREATE USER catalog WITH PASSWORD 'udacitynan';```
* ```ALTER USER catalog CREATEDB;```
* ```CREATE DATABASE mineralsitemsusers WITH OWNER catalog;```

Connect to the mineralsitemsusers database:

* ```\c mineralsitemsusers```

* ```REVOKE ALL ON SCHEMA public FROM public;```
* ```GRANT ALL ON SCHEMA public TO mineralsitemsusers;```


After install PostgreSQL, create the catalog user and the mineralsitemsusers database, is necessary to update the database setup and application python files to illustrate the new engine connection. For this purpose is necessary to change the database connections within the app from sqlite to Postgresql. This is achieved changing the following files of the cataloapp directory: project_database.py, populate_database.py and __init__.py. The command sudo nano /path/file.py allow for performing this changes using the nano text editor. 

* ```sudo nano project_database.py``` In line 72 of the project_database.py change the line engine = create_engine('sqlite:///mineralsitemsusers.db) to engine = create_engine('postgresql://catalog:udacitynan@localhost/mineralsitemsusers'). 

* ```sudo nano populate_database.py``` In line 12 of the file populate_database.py change the line engine = create_engine('sqlite:///mineralsitemsusers.db') to engine = create_engine('postgresql://catalog:udacitynan@localhost/mineralsitemsusers'). 

* ```sudo nano __init__.py``` In line 29 of the __init__.py file change the line engine = create_engine('sqlite:///mineralsitemsusers.db?''check_same_thread=False')
to engine = create_engine(postgresql://catalog:udacitynan@localhost/mineralsitemsusers')

* ```python project_database.py``` Create database schema 
* ```python populate_database.py Populate the database with some data 
* ```python __init__.py```  Run the app with and see if the application run correct.

# Configure and Enable a new Virtual Host

* sudo nano /etc/apache2/sites-available/catalogapp.conf Create catalogapp.conf file

Add the following lines of code to the file to configure the virtual host.

```
<VirtualHost *:80>
                ServerName 3.82.189.241
                ServerAdmin admin@3.82.189.241
                WSGIScriptAlias / /var/www/catalogapp/catalogapp.wsgi
                <Directory /var/www/catalogapp/catalogapp/>
                        Order allow,deny
                        Allow from all
                </Directory>
                Alias /static /var/www/catalogapp/catalogapp/static
                <Directory /var/www/catalogapp/catalogapp/static/>
                        Order allow,deny
                        Allow from all
                </Directory>
                ErrorLog ${APACHE_LOG_DIR}/error.log
                LogLevel warn
                CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost> 
```

* ```sudo a2ensite catalogapp``` Enable the virtual host 

# Create the .wsgi file 

Apache uses the .wsgi file to serve the catalogapp. Move to the /var/www/catalogapp directory and create a file named catalogapp.wsgi with following commands:

* ```cd /var/www/catalogapp```
* ```sudo nano catalogapp.wsgi```

Add the following lines of code to the catalogapp.wsgi file

```
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/catalogapp/")

from catalogapp import app as application
application.secret_key = 'Add your secret key'
```

Restart Apache to apply the changes with the command:

* ```sudo service apache2 restart``` 


# External resources to complete this project:  

https://www.digitalocean.com/community/tutorials/how-to-configure-ssh-key-based-authentication-on-a-linux-server

https://askubuntu.com/questions/138423/how-do-i-change-my-timezone-to-utc-gmt

https://www.digitalocean.com/community/tutorials/how-to-use-roles-and-manage-grant-permissions-in-postgresql-on-a-vps--2

https://www.cabotsolutions.com/2017/11/a-detailed-study-of-wsgi-web-server-gateway-interface-of-python




