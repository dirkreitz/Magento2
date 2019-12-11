# Introduction 

ATTENTION: The branches for PHP 5.6 and 7.4 will follow soon.
# Environment
This is a basic LAMP stack environment built using Docker Compose. It consists of the following:

* PHP
* Apache and Nginx (nginx for SSL offload)
* MySQL or MariaDB
* Magento2 (preinstalled via composer - curr. v2.4-develop)
* Redis (Database cache)
* Elasticsearch (v6.7.2)
* Varnish (v5.2.1-1) (HTTP accelerator)

Varnish being a reverse proxy caching server sits in front of Apache2 server.  Varnish is a HTTP accelerator - Varnish cannot deal with HTTPS traffic. Nginx serves as a reverse proxy server that receives traffic on port 80 and 443 and then proxy pass it to the listening port of the Varnish Cache server. In this configuration, we have generated private SSL certificates. You can use your own certificates and mention their path in nginx default configuration file.

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
* app-db,
* redis-db and
* es-db.

exec into the websrv container (easiest way is via 'Kitematic') or 'docker exec -it websrv bash'.

run the following command to log-in as the Magento File System owner

* su - magento

change into the '/app/bin' directory

run the following command to install the Magento software:

* ./magento setup:install --base-url=http://127.0.0.1/ --db-host=dbsrv --db-name=magento2 --db-user=admin --db-password=flensit --admin-firstname=Magento --admin-lastname=Admin --admin-email=user@example.com --admin-user=admin --admin-password=flensit1 --session-save-redis-host=redis --session-save-redis-port=6379 --use-rewrites=1 --language=de_DE --currency=EUR --timezone=Europe/Lisbon

run the following commands to finally setup the software

* ./magento setup:static-content:deploy -f de_DE da_DK en_GB en_US

run the following command to find out about the adminuri

* ./magento info:adminuri

write down the location and enter http://127.0.0.1/admin_xxxxx in your browser - start configuring your system !

Log in to the Magento Admin as an administrator (see above).

Click Stores > Settings > Configuration > Catalog > Catalog > Catalog Search.

Elasticsearch vers. 6.7.2 is installed - use Port 9200 and 'esearch' as host.

Click STORES > Settings > Configuration > ADVANCED > System > Full Page Cache to use Varnish.

## Redis
* ./magento setup:config:set --cache-backend=redis --cache-backend-redis-server=redis --cache-backend-redis-db=0