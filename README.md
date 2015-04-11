## Web server for Django App
This post provides a quick and easy way to set up web server for multiple Apps. It shows how to:
- Create simple app
- Set-up virtualhosts for simple app
- Create Django web app
- Configuring mod_wsgi virtual host

_My configuration:_  
- 1 VM on Google Compute Engine  
- OS: Debian 7 (Wheezy)  
- CPU: 1  
- Memory: 3.75 GB  
- External IP  

In the absence of a DNS configuration, I changed the /etc/hosts file (on my computer) in order that www.simple_app.com and www.django_app.com sign on my IP address

### 1 - Install packages
```sh
# Update packages source
apt-get update

# Install apache 
apt-get install apache2

# Cheick if server is listing
netstat -antp | grep apache2

# Install WSGI mode
aptitude install libapache2-mod-wsgi

# Install 
#apt-get install bzip2

# Install anaconda (Python distribution)
wget http://09c8d0b2229f813c1b93-c95ac804525aac4b6dba79b00b39d1d3.r79.cf1.rackcdn.com/Anaconda-2.0.1-Linux-x86_64.sh
bash Anaconda-2.0.1-Linux-x86_64.sh -b # -b stands for batch install

# Install pip
apt-get install python-pip 

# Install virtualenv
python-virtualenv
```

### 2 - Create simple web app
```sh
# Add user
sudo su
adduser simple_app_user

# Create repository to store app file
mkdir -r /home/simple_app_user/app/public_html/

# Create link to /var/www/
cd /var/www/
ln -s /home/simple_app_user/app/public_html/ classical_app

# Create index.html file 
touch /var/www/simple_app/index.html
echo '<html><body><h1>Hello simple app!</h1></body></html>' > /var/www/simple_app/index.html
```

### 3 - Set up virtualHost to deploy classical app
```sh
# Change directory
cd /etc/apache2/sites-available/

# Create configuration file
cp default www.simple_app.com

# Change file configuration
nano  www.simple_app.com
"""
<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        ServerName www.simple_app.com
        
        DocumentRoot /var/www/simple_app/
        <Directory />
                Options FollowSymLinks
                AllowOverride all 
        </Directory>
        <Directory /var/www/simple_app/>
                Options Indexes FollowSymLinks MultiViews
                AllowOverride all 
                Order allow,deny
                allow from all
        </Directory>

        ScriptAlias /cgi-bin/ /usr/lib/cgi-bin/
        <Directory "/usr/lib/cgi-bin">
                AllowOverride None
                Options +ExecCGI -MultiViews +SymLinksIfOwnerMatch
                Order allow,deny
                Allow from all
        </Directory>

        ErrorLog /var/log/apache2/error.log

        # Possible values include: debug, info, notice, warn, error, crit,
        # alert, emerg.
        LogLevel warn

        CustomLog /var/log/apache2/access.log combined

    Alias /doc/ "/usr/share/doc/"
    <Directory "/usr/share/doc/">
        Options Indexes MultiViews FollowSymLinks
        AllowOverride None
        Order deny,allow
        Deny from all
        Allow from 127.0.0.0/255.0.0.0 ::1/128
    </Directory>

</VirtualHost>
"""

# Activate VirtualHosts
cd ~
cd /etc/apache2/sites-enabled
ln -s ../sites-available/www.classical_app.com .

# Restart server
/etc/init.d/apache2 restrart
```
Cheik **www.classical_app.com** on your browser

### 4 - Create Django web app
```sh
# Add user
sudo su
adduser django_app_user

# Create repository to store app file
mkdir -r /home/django_app_user/app/public_html/

# Create link to /var/www/
cd /var/www/
ln -s /home/django_app_user/app/public_html/ django_app

# Create virtualenv
cd /var/www/django_app
virtualenv django_env

# Activate virtualenv
source django-env/bin/activate 

# Cheick if pip exist
pip --version

# Desactivate virtualenv
deactivate

# Install Django
django-env/bin/pip install django

# Test Django installaton
django-env/bin/django --version

# Create Django App toto-app3
source django-env/bin/activate
django-admin.py startproject web_app_django
```

# 5 - Configuring mod_wsgi virtual host
```
# Change directory
cd /etc/apache2/sites-available/

# Create configuration file
cp default www.django_app.com

nano /etc/apache2/sites-available/www.django_app.com
"""
<VirtualHost *:80>
    WSGIDaemonProcess eurecia python-path=/var/www/django_app:/var/www/django_app/django_env/lib/python2.7/site-packages
    WSGIProcessGroup web_app_django 
    WSGIScriptAlias / /var/www/django_app/web_app_django/web_app_django/wsgi.py 
    #WSGIScriptPath /var/www/myapp/eurecia/ 

    ServerAdmin webmaster@localhost 
    ServerName www.django_app.com 
    #DocumentRoot /var/www/myapp 

    <Directory /var/www/django_app/web_app_django/>
        <Files wsgi.py> 
            Order deny,allow 
            Allow from all 
        </Files> 
    </Directory> 

    ErrorLog ${APACHE_LOG_DIR}/error.log 
    # Possible values include: debug, info, notice, warn, error, crit, 
    # alert, emerg. 
    LogLevel warn 
    
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

# Activate VirtualHosts
cd ~
cd /etc/apache2/sites-enabled
ln -s ../sites-available/www.django_app.com .

# Restart server
/etc/init.d/apache2 restrart
```

Cheik **www.django_app.com** on your browser




