# Udacity-Project-6
In this project we configure and secure a Linux server and hosted our item catalog from Project 4

IP Address: 52.55.254.90  
Port: 2200  
URL: http://52.55.254.90.xip.io/  
SSH: ssh grader@52.55.254.90 -p 2200 -i ~/.ssh/graderkey

SSH key was submitted in notes to reviewer field

## Checklist from Project Details Page

### Get your server
1. Start a new Ubuntu Linux server instance on Amazon Lightsail. There are full details on setting up your Lightsail instance on the next page.
	* https://lightsail.aws.amazon.com/ls/webapp/home/instances
	* Create instance
	* Ubuntu 16.04
2. Follow the instructions provided to SSH into your server.

### Secure your server
3. Update all currently installed packages.
	* `sudo apt-get update`
	* `sudo apt-get upgrade`
4. Change the SSH port from 22 to 2200. Make sure to configure the Lightsail firewall to allow it.
	* Configured on instances networking page
5. Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123).
	* In `sudo nano /etc/ssh/sshd_config` change port to 2200
	* `sudo ufw default deny incoming`
	* `sudo ufw default allow outgoing`
	* `sudo ufw allow 80`
	* `sudo ufw allow 2200`
	* `sudo ufw allow 123`
	* `sudo ufw enable`

### Give grader access
6. Create a new user account named grader.
	* `sudo adduser grader`
7. Give grader the permission to sudo.
	* `sudo touch /etc/sudoers.d/grader`
	* `sudo nano /etc/sudoers.d/grader`
	* add line `grader ALL=(ALL) NOPASSWD:ALL`
8. Create an SSH key pair for grader using the ssh-keygen tool.
	* Created key pair by running `ssh-keygen` locally
	* Uploaded public key to AWS account page

### Prepare to deploy your project
9. Configure the local timezone to UTC.
	* `sudo timedatectl set-timezone UTC`
10. Install and configure Apache to serve a Python mod_wsgi application.
	* If you built your project with Python 3, you will need to install the Python 3 mod_wsgi package on your server: sudo apt-get install libapache2-mod-wsgi-py3.
	* `sudo apt-get install apache2`
	* `sudo apt-get install libapache2-mod-wsgi-py3`
	* `sudo touch /etc/apache2/sites-enabled/catalog.conf`
	* `sudo nano /etc/apache2/sites-enabled/catalog.conf`
		* ```<VirtualHost *:80>
  				ServerName 52.55.254.90
  				ServerAdmin mavaughn07@gmail.com
  				WSGIDaemonProcess views user=grader python-home=/var/www/catalog python-path=$
  				WSGIScriptAlias / /var/www/catalog/wsgi.py
  				<Directory /var/www/catalog/>
    				WSGIProcessGroup views
    				WSGIApplicationGroup %{GLOBAL}
    				Require all granted
  				</Directory>
  				ErrorLog ${APACHE_LOG_DIR}/error.log
  				LogLevel warn
  				CustomLog ${APACHE_LOG_DIR}/access.log combined
			</VirtualHost>```
		* I had issuse with what I thought was my configuration here, so I may have unnecessary information. Turns out that I had my import the wrong way around in my wsgi file
	* `sudo a2ensite catalog`
	* added **wsgi.py** to /var/www/catalog configured as below
		* ```import sys
				sys.path.insert(0, "/var/www/catalog")

				from views import app as application

				if __name__ == '__main__':
    				application.config['SESSION_TYPE'] = 'filesystem'
    				application.run(host='0.0.0.0', port=8000)``` 
		* This is where my biggest trouble happened. My original file had the flask app from views being imported before /var/www/catalog was inserted to the path. 

11. Install and configure PostgreSQL:
	* Do not allow remote connections
	* Create a new database user named catalog that has limited permissions to your catalog application database.
	* Since my database uses SQLite, I skipped this step in the server. If I were to configure, the steps would be below:
		* `sudo apt-get install postgresql`
    	* `sudo su catalog -c 'createdb itemCatalog`
		* `sudo su postgres -c 'psql'`
		* `REVOKE connect ON DATABASE itemCatalog FROM PUBLIC;`
		* `CREATE USER catalog with PASSWORD '[password]';`
		* `GRANT connect ON DATABASE itemcatalog TO catalog;`
		* I would then go in and change my SQLite engine creation in **views.py**, **models.py** and **createCatalog.py** to point to the new database, with username and password as arguments.
12. Install git.
	* `sudo apt-get install git`

### Deploy the Item Catalog project
13. Clone and setup your Item Catalog project from the Github repository you created earlier in this Nanodegree program.
	* `sudo git clone [project link] /var/www/catalog`
14. Set it up in your server so that it functions correctly when visiting your server’s IP address in a browser. Make sure that your .git directory is not publicly accessible via a browser!
	* Verified configuration 


### Aditional Steps
* Disabled root access
	* `sudo nano /etc/ssh/sshd_config`
	* changed to `PermitRootLogin no`
* Forced key-base login
	* `sudo nano /etc/ssh/sshd_config`
	* changed to `PasswordAuthentication no`
* Changed references in the **views.py** **models.py** and **createCatalog.py** to absolute pathing for the **client-secrets.json** and **itemCatalog.db** files
	* `/var/www/catalog/client_secrets.json'`
	* `engine = create_engine('sqlite:////var/www/catalog/itemCatalog.db')`
* Moved the **app.config['SECRET_KEY']** out of __main__ since it is not created
* Changed the `/var/www/catalog` owner to grader, so database was no longer read only
	* `sudo chown grader /var/www/catalog` 
* Added authorised domain of xip.io in Google's console, so the log in function worked correctly

### Software:
* Amazon Lightsail Ubuntu 16.04 server
* Python3
	* `sudo apt-get install python3-pip`
* Various Python Modules
	* `sudo pip3 install flask packaging oauth2client redis passlib flask-httpauth`
	* `sudo pip3 install sqlalchemy flask-sqlalchemy psycopg2-binary bleach requests`

### Third-Party Resources:
* StackOverflow.com
* DigitalOcean.com
* Udacity Videos