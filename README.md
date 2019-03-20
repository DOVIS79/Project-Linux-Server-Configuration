# Linux Server Configuration Project by Jesse Garcia
This project explored the configuration of a Linux machine as a server for the Item Catalog Project, including the deployment of an Amazon AWS Lightsail instance, SSH private key connection, configuration of the instance with the required packages, and deploying the python application as a webserver.

## Connection Details
**IP Address: ** 3.209.141.234
**SSH Port: ** 2200
**Webserver URL:** http://3.209.141.234/

## Configuration Summary
- An Amazon Lightsail instance is created (Linux, OS only). The instance is created and associated with a new keypair that I download and save.
- Through the Lightsail website, I configure the firewall to allow traffic on port 80, since this is the port I have configured the HTTP server to serve on.
- I connect to the SSH server with the keypair saved. No password is needed since I using the private key authentication.
```$ chmod 400 GraderKeyPair.pem
$ ssh grader@3.209.141.234 -i GraderKeyPair.pem
```
- Allow `grader` user to run commands as `root` with `sudo` by adding the user to the group:
`$ usermod -a -G sudo grader`
- Access the SSH server configuration file and and modify the following lines:
```$ sudo vim /etc/ssh/sshd_config
    # Disallow login to root account via SSH
    PermitRootLogin no
    # Change port from 22 to 2200
    Port 2200
    # Enforce key-based authentication for SSH login
    PasswordAuthentication no
```
- Restart the SSH server to apply changes
`$ sudo service sshd restart`
- From now on we will have to connect like this
`$ ssh grader@3.209.141.234 -p 2200 -i GraderKeyPair.pem`
- Upgrade the system's packages to the most up-to-date version for security reasons
`$ sudo apt update && sudo apt upgrade`
- Clone the project files onto the instance
`$ git clone https://github.com/DOVIS79/catalog.git`
- Move into the new directory
`$ cd catalog`
- Make directories for the templates and static files
`$ mkdir templates && mkdir static`
- Move the project files into the correct directories
`$ mv *.{jpg,png,css} static/ && mv *.html templates/`
- Python 2.7.15 is installed
`$ sudo apt update && sudo apt install python`
- Run the configuration script for the server. This installs PostgreSQL, python-psycopg2, python-flask, python-sqlalchemy, and python-pip. Then, pip is used to install the following packages: bleach, oauth2client, requests, httplib2, redis, passlib, itsdangerous, and flask-httpauth
`$ sudo sh pg_config.sh`
- The initialization python scripts are run to setup and populate the database
`$ python database_setup.py && python feedcatalog.py`
- Run the main python application using screen, so that the server stays running after we exit the SSH session
`$ screen sudo python main.py # We need sudo to bind to port 80`
- The server is fully configured and up and running at on port 8000
