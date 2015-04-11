## Web server for Django App
This post provides a quick and easy way to set up web server for multiple Django App. It shows how to:
- Install gsutils tools


_My configuration:_  
1 VM on Google Compute Engine
OS: Debian 7 (Wheezy)
CPU: 1
Memory: 3.75 GB
External IP

### 1 - Set up env 
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

# Add user
sudo su
adduser toto

# Create repository for each app to store app file
mkdir /var/www/toto-app1
mkdir /var/www/toto-app2
mkdir -r /home/toto/toto-app3/public_html/

# Create index file for each app
touch /var/www/toto-app1/index.html
touch /var/www/toto-app2/index.html
touch /home/toto/toto-app3/public_html/index.html

echo '<html><body><h1>Hello toto-app1!</h1></body></html>' > /var/www/toto-app1/index.html
echo '<html><body><h1>Hello toto-app2!</h1></body></html>' > /var/www/toto-app2/index.html
echo '<html><body><h1>Hello toto-app3!</h1></body></html>' > /home/toto/toto-app3/public_html/index.html

# link toto-app3 to /var/www/
cd /var/www/
ln -s /home/toto/toto-app3 toto-app3
```

### Set up virtualHost to store multiple apps on the same server
```sh
# Change directory
cd /etc/apache2/sites-available/

# Create configuration file for each app
cp default www.toto-app1.com
cp default www.toto-app2.com

# Change file configuration
nano  www.toto-app1.com
"""
<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        ServerName www.toto-app1.com
        
        DocumentRoot /var/www/toto-app1/
        <Directory />
                Options FollowSymLinks
                AllowOverride all 
        </Directory>
        <Directory /var/www/toto-app1/>
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

nano  www.toto-app2.com
"""
<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        ServerName www.toto-app1.com
        
        DocumentRoot /var/www/toto-app2/
        <Directory />
                Options FollowSymLinks
                AllowOverride all 
        </Directory>
        <Directory /var/www/toto-app2/>
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
ln -s ../sites-available/toto-app1 .
ln -s ../sites-available/toto-app2 .

cd /var

# Restart server
/etc/init.d/apache2 restrart

```
### Set-up virtualenv for Django App
```sh
# Create virtualenv
virtualenv /var/www/toto-app3/django-env

# Activate virtualenv
source /var/www/toto-app3/django-env/bin/activate 

# test if pip exist
pip --version

# Desactivate virtualenv
deactivate






