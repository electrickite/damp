damp
====

Bash script to simplify local Drupal development using the 
Drupal/Apache/MySQL/PHP (DAMP) stack.

Operations
----------

list/info - Shows a list of installed sites or details about a particular site  
new - create a new site  
open - open a site in a web browser  
install - open the web-based site installer
remove - remove a site  
set - set site/global options  
start - start services  
stop - stop services  
restart - restart services  
status - show status of services  
setup - install and configure DAMP stack  
configure - edit configuration files  
fixperms - fix site directory permissions  

Options
-------

name - the site shortname  
domain - the site domain  
docroot - the site document root  
database - the database name  

dbuser - the database user  
dbpass - the database user password  

y - answer yes to all prompts  
mail - install MTA during setup  
phpconf - php conf file path  
httpdconf - the httpd configuration file path  
httpdvhost - the vhost configuration file directory  
httpdport - the httpd port number  
dbconf - the mysql configuration file path  
dbport - the database port number  
initpath - path to service init scripts  
lang - language to use during install  

Examples
--------

damp open foo  
damp set --domain example.com mysite  
damp configure php  
