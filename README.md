damp
====

Bash script to simplify local Drupal development using the 
Drupal/Apache/MySQL/PHP (DAMP) stack.

Operaions
---------
  list - Shows a list of all installed sites
  list/info - get details about a site
  new - create a new site
  open - open a site in a web browser
  remove - remove a site
  update - sync services configuration with installed sites
  set - set variables for a site
  start - start services
  stop - stop services
  restart - restart services
  status - show status of services
  setup - install and configure packages
  global - set global options
  conf - edit configuration files

Options
-------
  name - the site shortname
  domain - the site domain
  path - the site document root
  db/database - the database name

  phpconf - php conf file path
  httpdconf - the httpd configuration directory path
  mysqlconf - the mysql configuration file path
  httpdport - the httpd port number
  dbuser - the database root user
  dbpass - the database root user password
  dbport - the database port number
  initpath - path to service init scripts

Examples
--------

damp open foo
damp set --path /bar/baz --domain localhost foo
damp conf php
