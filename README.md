# Udacity-Project-6
In this project we configure and secure a Linux server and hosted our item catalog from Project 4

IP Address: 52.55.254.90  
Port: 2200  
URL: http://52.55.254.90.xip.io/  
Software:
* Amazon Lightsail Ubuntu 16.04 server
Third-Party Resources:
* StackOverflow.com
* DigitalOcean.com

SSH key was submitted in notes to reviewer field

## Checklist from Project Details Page

### Get your server
1. Start a new Ubuntu Linux server instance on Amazon Lightsail. There are full details on setting up your Lightsail instance on the next page.
2. Follow the instructions provided to SSH into your server.

### Secure your server
3. Update all currently installed packages.
4. Change the SSH port from 22 to 2200. Make sure to configure the Lightsail firewall to allow it.
5. Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123).

### Give grader access
6. Create a new user account named grader.
7. Give grader the permission to sudo.
8. Create an SSH key pair for grader using the ssh-keygen tool.

### Prepare to deploy your project
9. Configure the local timezone to UTC.
10. Install and configure Apache to serve a Python mod_wsgi application.
	* If you built your project with Python 3, you will need to install the Python 3 mod_wsgi package on your server: sudo apt-get install libapache2-mod-wsgi-py3.
11. Install and configure PostgreSQL:
	* Do not allow remote connections
	* Create a new database user named catalog that has limited permissions to your catalog application database.
12. Install git.

### Deploy the Item Catalog project
13. Clone and setup your Item Catalog project from the Github repository you created earlier in this Nanodegree program.
14. Set it up in your server so that it functions correctly when visiting your server’s IP address in a browser. Make sure that your .git directory is not publicly accessible via a browser!

### Aditional Steps
* Disabled root access
* Forced key-base login
* Apache configuration
* WSGI file setup
* Changed references in the **views.py** **models.py** and **createCatalog.py** to absolute pathing for the **client-secrets.json** and **itemCatalog.db** files
	* `/var/www/catalog/client_secrets.json'`
	* `engine = create_engine('sqlite:////var/www/catalog/itemCatalog.db')`
* Moved the **app.config['SECRET_KEY']** out of __main__ since it is not created
* Changed the `/var/www/catalog` owner to grader, so database was no longer read only
* Added authorised domain of xip.io in Google's console, so the log in function worked correctly
