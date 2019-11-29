# Introduction 

ATTENTION: currently only 'Apache' is implemented. Varnish and Hitch is 'work in progress' and will not be installed. The branches for PHP 5.6 and 7.4 will follow soon.
# Environment
This is a basic LAMP stack environment built using Docker Compose. It consists following:

* PHP
* Apache or Nginx
* MySQL or MariaDB
* Magento2 (preinstalled via composer)
* Redis
* Varnish
* Hitch

As of now, we have different branches for different PHP versions. Use appropiate branch as per your php version need:

* 5.6.x
* 7.3.x

# Build and Test
Clone this repository on your local computer and checkout the appropriate branch e.g. 7.3.x.
Edit the example.env file to your needs.
Copy example.env to .env (cp example.env .env)
Run the "docker-compose up -d" command.

Configuration changes could be applied by editing the files in the 'conf'-directory. You would have to restart the corresponding container. Data is stored in persistent volumes - they are named
* app-data,
* app-log,
* app-db and
* redis-db.

exec into the websrv container

run the following command to install the Magento File System owner

* adduser --home /app --ingroup www-data magento

change into the '/app/bin' directory

run the following command to install the Magento software:

* ./magento setup:install --base-url=http://127.0.0.1/magento2/ --db-host=dbsrv --db-name=magento2 --db-user=admin --db-password=flensit --admin-firstname=Magento --admin-lastname=Admin --admin-email=user@example.com --admin-user=admin --admin-password=flensit1 --session-save-redis-host=redis --session-save-redis-port=6379 --use-rewrites=1 --language=de_DE --currency=EUR --timezone=Europe/Lisbon

run the following commands to finally setup the software

* ./magento setup:static-content:deploy -f de_DE da_DK en_GB en_US
* ./magento setup:upgrade
* ./magento cache:clean
* ./magento cache:flush