# Linux Server Configuration Project

## Description
Setup Ubuntu server to serve a Flask app (Catalog)

## Access to server
- IP: 18.196.124.97
- SSH Port: 2200
- Username: grader
- SSH Public Key: Will be provided in reviewer notes while submission

## Web app working URL
http://18.196.124.97.xip.io/
> note that only Google sign-in is working as Facebook requires HTTPS to make an OAuth login

> Tried using Let's Encrypt but it doesn't support IP, only domains

## Installed software
- Postgresql for database management (Refer to https://www.digitalocean.com)
- Apache2 to serve Catalog app
- Virtualenv to setup a python virtual environment to isolate the app from the system

## Configurations made
- Registered for an AWS Lightsail server instance and chose the minimal plan
- Added port 2200 as Custom and made sure it's accessible
- Disable `PAssword Authentication` and `PermitRootLogin
` from `/etc/ssh/sshd_config` so only users with SSH Public keys can login and Disable Root log-in
- Removed default SSH port 22 from Lightsail security and `/etc/ssh/sshd_config`
- Installed Apache2 and displayed the test page successfully
- Created `grader` user and added it to sudo group
- Locally generated an SSH key for `grader` and added to server's `.ssh/authorized_keys` and tested logging in
- Cloned `Catalog App` from Github into `/var/www/catalog`
- Created a Virtual environment using Virtualenv and installed all needed Packages
- Created the WSGI file that runs app files
- Updated `catalog_project.py` into `__init__.py` in order to start automatically and updated line `app.run(host='0.0.0.0', port=8000)` to be `app.run()`
- Installed PostgreSQL
- Created a `ROLE` named `catalog` with password `catalog`
- Created database named `catalog` and Granted all privileges to `catalog` User
- Updated my app project files with the new database credentials (database_setup.py & crud_functions.py)
- Updated `fb_secrets` and `client_secrets` files locations in app code (Required for Auth login)
- Got a wildcard domain from `xip.io` to add to Google OAuth
- added new file to `/etc/apache2/sites-available/` called `catalog.conf` including my app configurations

```
<VirtualHost *:80>
    ServerName 18.196.124.97.xip.io
  ServerAlias ec2-18-196-124-97.eu-central-1.compute.amazonaws.com
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
RewriteEngine on
RewriteCond %{SERVER_NAME} =ec2-18-196-124-97.eu-central-1.compute.amazonaws.com [OR]
RewriteCond %{SERVER_NAME} =18.196.124.97 [OR]
</VirtualHost>

```

- Disabled the default apache profile `000-default.conf` and applied the new configuration file `catalog.conf` to enabled-sites:
> a2dissite 000-default.conf

> sudo a2ensite catalog.conf

> service apache2 restart
