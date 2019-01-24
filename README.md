# Linux-Server-Configuration

I would describe the steps in order to set up and configure a Linux Server, based on an instance of lightsail Amazon Web Service. This was part of the Full Stack Developer Nano Degree course from Udacity and the project was Linux Server Configuration. 


Information about Lightsail Linux Instance:

Public IP Address: 3.82.189.241 

Port: 2200 

## Amazon Lightsail instance

Create an instance on Amazon Lightsail in Linux platform with Ubuntu 16.04 LTS. Once you get the instance, connected using the Orange button Connecting Using SSH. When reach to the Linux platform is necessary to made some steps configuration described below that will be done with the ubuntu default user. These steps are required in order to upgrade Linux packages and configure the Firewall required of this project: 

sudo apt-get upgrade

sudo apt-get update 

add the 2200 port in the etc/ssh/sshd_config file in the line below of port 22(default port)

on the Amazon Ligthsail web site, add a custom port 2200 in the Networking tab 

sudo ufw default deny incoming (by default deny all incoming connections)

sudo ufw default allow outgoing (default to deny  outgoing connections)

Configure firewalls rules:

sudo ufw allow ssh

sudo ufw allow 2200/tcp (allow all TCP connections through port 2200)

sudo ufw allow 80/tcp

sudo ufw allow 123 

sudo ufw enable (to enable the firewall)


## Create the Grader User 

The following commands will be executed in order to create the grader user and allow ssh key authentication connection for the grader user to the server. This consist of a public and private key that work together. The public key would be copy to the server and the private key is the key that the user store in it computer and use to connect to the server. The public key would be copy to the /home/grader/.ssh/authorized_keys file. The commands are the following:

The following command should be running from the server: 

sudo add grader. Create the grader user

sudo usermod -a -G sudo grader. This give sudo permissions to the grader user 

su - grader. To change to the grader user in the Linux screen

mkdir .ssh. Create the .ssh dirctory in Linux

touch .ssh/authorized_keys. Create the file authorized_keys under the .ssh dirctory

From your local computer run the following command: 

ssh-keygen. 

This command generate two keys, one public and one private. The public key has to be copy to the authorized_keys file on the Linux instance. Finally change permissions to the .ssh directory and the authorized_keys:

chmod 700 /home/grader/.ssh

chmod 644 /home/grader/.ssh/authorized_keys

Finally, change the owner of the directory and authorized_keys file with:
sudo chown grader .ssh

service ssh restart

now you can use ssh to login with the new user you created

ssh -i [privateKeyFilename] grader@3.82.189.241 

After run this command, open the public key generated in your computer and copy it. Afterwards createI install finger tool to check the users that can access the server. Then I created the grader user with sudo add grader and give it sudo  privileges. In order to allow ssh authentication to the grader user I use the tool ssh-keygen on my machine and paste the public key on the authorized_keys file under the /home/grader/.ssh directory. I change permissions to the .ssh directory and the 
authorized_keys with 

# Application Functionality

This app would be deployed using Flask, Apache2 and Postgresql in Linux remote system using Amazon Lightsail. The following  steps consists on the configuration of a database and a web server as well as clone the Flask application to the Linux instance. The WSGI module will be used as an interface between Flask and Apache Server.  

Install and configure Apache to serve a Python mod_wsgi application

Install Apache sudo apt-get install apache2

Install mod_wsgi sudo apt-get install libapache2-mod-wsgi-py3

Install git, clone and setup your Catalog App project

Install Git using sudo apt-get install git

Use cd /var/www to move to the /var/www directory

Create the application directory sudo mkdir catalogapp

Move inside this directory using cd Catalogapp

Clone the Catalog App to the virtual machine git clone https://github.com/nicolasferrari/catalogapp.git catalogapp


Move into the /var/www/catalogapp/catalogapp 

Rename application.py file to __init__.py using sudo mv application.py __init__.py


Edit database_setup.py, website.py and functions_helper.py and change engine = create_engine('sqlite:///toyshop.db') to engine = 

create_engine('postgresql://catalog:password@localhost/catalog')

Install pip sudo apt-get install python-pip

Use pip to install dependencies sudo pip install -r requirements.txt

Install psycopg2 sudo apt-get -qqy install postgresql python-psycopg2

Create database schema sudo python database_setup.py

To get connection to the server with the grader account is necessary to run this command : ssh grader@3.82.189.241 -i ~/.ssh/key.pub

Installation of Apache2, WSGI middleware Postgresql:

I have installed these software with the following commands:

sudo apt-get install apache2

sudo apt-get install libapache2-mod-wsgi

sudo apt-get install postgresql

Change the /etc/apache2/sites-enabled/000-default.conf file with the command sudo nano /etc/apache2/sites-enabled/000-default.conf by adding the line WSGIScriptAlias / /var/www/html/myapp.wsgi. With this procedure I ensure that the app is in the default directory that Ubuntu recognize to store applications. 

Some external resources that I use to made all this are: 

https://www.digitalocean.com/community/tutorials/how-to-configure-ssh-key-based-authentication-on-a-linux-server

https://askubuntu.com/questions/138423/how-do-i-change-my-timezone-to-utc-gmt

https://www.digitalocean.com/community/tutorials/how-to-use-roles-and-manage-grant-permissions-in-postgresql-on-a-vps--2

https://www.cabotsolutions.com/2017/11/a-detailed-study-of-wsgi-web-server-gateway-interface-of-python

3- The public key that I have created for the grader authentication within the server is located on /home/grader/.ssh/authorized_keys.


