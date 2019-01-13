# Linux-Server-Configuration

I would describe the steps in order to set up and configure a Linux Server, based on an instance of lightsail Amazon Web Service. After that I would install apache 2 and postgresql server with the aim to host and deploy the item-catalog application.

Information about Lightsail Linux Instance:

Public IP Address: 3.82.189.241 Port: 2200 

Once I get the Lightsail Ubuntu instance from Amazon Web Service, I have upgraded all the packages that this distribution has with the command sudo apt-get upgrade. Then I proceed to configure the Firewalls by adding a custom port 2200 in the Networking tab of the amazon web service site account. In order to allow this port I have to added it in the sshd_config file that is within the etc/ssh/sshd_confif file. The default port on this file was port 22, and in my case I added Port 2200 below Port 22.

Afterwards I run a set of commands in order to change the status of UFW by adding some rules to the firewall and allow incoming connectios from  port 2200/tcp(SSH) as well as port 80(HTTP) and port 123(NTP). These commands consist on: 

sudo ufw default deny incoming (by default deny all incoming connections)

sudo ufw default allow outgoing (default to deny  outgoing connections)

Configure firewalls rules:

sudo ufw allow ssh

sudo ufw allow 2200/tcp (allow all TCP connections through port 2200)

sudo ufw allow 80/tcp

sudo ufw allow 123 

sudo ufw enable (to enable the firewall)

I install finger tool to check the users that can access the server. Then I created the grader user with sudo add grader and give it sudo  privileges. In order to allow key pair authentication to the grader user I use the tool ssh-keygen on my machine and past the public key on the authorized_keys file under the /home/grader/.ssh directory. I change permissions to the .ssh directory and the 
authorized_keys with 

chmod 700 /home/grader/.ssh

chmod 644 /home/grader/.ssh/authorized_keys

Finally I have to change the owner of the directory and authorized_keys file with:
sudo chown grader .ssh

To get connection to the server with the grader account is necessary to run this command : ssh grader@3.82.189.241 -i ~/.ssh/key.pub

Some external resources that I use to made all this are: 

https://www.digitalocean.com/community/tutorials/how-to-configure-ssh-key-based-authentication-on-a-linux-server

https://askubuntu.com/questions/138423/how-do-i-change-my-timezone-to-utc-gmt

https://www.digitalocean.com/community/tutorials/how-to-use-roles-and-manage-grant-permissions-in-postgresql-on-a-vps--2

https://www.cabotsolutions.com/2017/11/a-detailed-study-of-wsgi-web-server-gateway-interface-of-python

3- The public key that I have created for the grader authentication within the server is located on /home/grader/.ssh/authorized_keys.


