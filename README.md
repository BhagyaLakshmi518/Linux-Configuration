Project 6 Linux-Server-Configuration
#### Details
  Server Ip Address:13.232.93.198
  site url: http://13.232.93.198.xip.io
#### Steps to connect to grader:
      we have to creater a new user called grader with the following command:
             sudo adduser grader
      In order to grant permissions to the grader we have to open a nano file with the following command:
              sudo nano /etc/sudoers
       to grant permissions we have to add the following line to the nano file:
               grader  ALL=(ALL:ALL) ALL
      while creating our instance in lightsail we have to download the default file.
      install putty in your computer and generate a private key using the previously downloaded default file
      we have to create a new ssh key-pair for grader with the following command:
                ssh-keygen
      The generated keys will be stored in .ssh folder
      In virtual machine(putty) change to user grader:
            su - grader
       Then create a new directory .ssh:
          mkdir .ssh
       Then in that create a file names authorized_keys:
            sudo nano .ssh/authorized_keys
        Then give permissions to the newly created directory:
             chmod 700 .ssh
        We have to copy the key generated and stored in .ssh folder to the authorized_keys file and save it
        prevent others from writing into the file:
              chmod 644 .ssh/authorized_keys
#### run the following command:
            service ssh restart
       Then log in to grader with your privatekey generated
              ssh -i .ssh/id_rsa grader@ipaddress
       we have to change the port to 2200 with the following code:
            sudo nano /etc/ssh/sshd_config
        restart the ssh server:
              service ssh restart
       then we have to add this port 2200 in the lightsail networkings tab for firewall safety
       Login into grader using the below command:
              ssh -i .ssh/id_rsa -p 2200 grader@ipaddress
        Then run the following command:
              sudo nano /etc/ssh/sshd_config
         Firewall safety commands:
                sudo ufw allow 2200/tcp
                sudo ufw allow 80/tcp
                sudo ufw allow 123/udp
                sudo ufw enable
        run "sudo ufw status"
#### Installing Apache2:
        sudo apt-get install apache2
        sudo apt-get install python-setuptools libapache2-mod-wsgi
        sudo a2enmod wsgi
      We have to create a flaskapp:
          sudo mkdir FlaskApp
          cd FlaskApp
     Install git:
            sudo apt-get install git
      get the app you have to deploy from your github account:
            sudo git clone https://github.com/name/item_catalog.git
       Change the main file name to __init__.py:
            sudo mv project.py __init__.py
       open init file and add the json file:
           sudo nano __init__.py
          PROJECT_ROOT = os.path.realpath(os.path.dirname(__file__))
          json_url = os.path.join(PROJECT_ROOT, 'client_secrets.json')
        then save it and exit:
             ctrl+o
             ctrl+x
         Create a wsgi file:
                sudo nano mv project.py FlaskApp.wsgi
          Add the following code into the wsgi file:
                #!/usr/bin/python
                import sys
                import logging
                logging.basicConfig(stream=sys.stderr)
                sys.path.insert(0,"/var/www/FlaskApp/")
                from FlaskApp import app as application
                application.secret_key = 'Add your secret key'
          create a cofiguration file:
                sudo nano /etc/apache2/sites-available/FlaskApp.conf
           Add the following code into the .conf file:
                  <VirtualHost *:80>
                  ServerName mywebsite.com
                  ServerAdmin admin@mywebsite.com
                  WSGIScriptAlias / /var/www/FlaskApp/flaskapp.wsgi
                  <Directory /var/www/FlaskApp/FlaskApp/>
                    Order allow,deny
                    Allow from all
                  </Directory>
                  Alias /static /var/www/FlaskApp/FlaskApp/static
                  <Directory /var/www/FlaskApp/FlaskApp/static/>
                    Order allow,deny
                    Allow from all
                  </Directory>
                  ErrorLog ${APACHE_LOG_DIR}/error.log
                  LogLevel warn
                  CustomLog ${APACHE_LOG_DIR}/access.log combined
                    </VirtualHost>
           In order to run the app we have to install the following softwares :
                    pip install sqlalchemy
                    pip install flask
                    pip install oauth2client
                    pip install pyscopg2
                    pip install requests
            
#### Install postgresql:
        sudo apt-get install postgresql
#### login to postgres:
         sudo su - postgres
         psql
#### create a new user in postgres:
        CREATE USER catalog WITH PASSWORD 'password';
#### give the user permissions to create a new database:
         ALTER USER item_catalog CREATEDB;
 #### to create a new database with user :
        CREATE DATABASE catalog WITH user catalog;
#### connect to db:
        \c catalog
#### give permissions to public:
        REVOKE ALL ON SCHEMA public FROM public;
#### grant permissions on schema for catalog:
        GRANT ALL ON SCHEMA public TO catalog;
       Example for deploying a flaskapplication can be known from digitalOcean
     Now login to your developer console and select your project and edit Oauth details:
     Javascript origin 
        http://ip.xip.io
     redirect URI
      http://ip.xip.io\login
      http://ip.xip.io\gconnect
      http://ip.xip.io\callback
      xip.io is a free DNS which will be the same as using IP address
#### Final Step:
       sudo service apache2 restart
  
