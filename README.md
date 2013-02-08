DAMP
====

DAMP is a bash script intended to simplify local [Drupal][1] development using
the Drupal/Apache/MySQL/PHP (DAMP) stack. The goal of the project is to provide
an easy-to-use interface for installing a local web stack and manage Drupal
sites on top it. [Acquia Dev Desktop][2] already provides this type of
environment for Mac and Windows users, but I could not find a similar utility
for GNU/Linux.

As a bash script, damp has no graphical interface: all operations are performed
in the terminal. This can be a real advantage if you need to perform a number of
operations over and over, such as installing a new site to develop an
installation profile, but is not a intuitive as the Dev Desktop GUI.

The script is intended to run on either Debian/Ubuntu or RedHat/CentOS. It has
been tested on recent versions of these distributions and will likely not work
on other/older distros. Patches to improve portability are welcome!

Notes
-----

  * DAMP is intended for use in local development only: you should not use this
    script in a production environment. It is _not_ designed with security or
    prformance in mind. Permissions are deliberately relaxed, passwords may be
    stored in the clear, etc. Use at your own risk
  * DAMP uses real web hosting software, so you should treat your system like a
    web server while using DAMP. This means operating a host-based firewall is
    strongly advised.
  * While the script attempts to correct some common permissions and security
    issues, you may ultimately have to adjust the permissions on your Drupal
    durectories to ensure that Apache can access them. Please see your
    distribution's documentation to help solve these issues.
  * The script attempts to configure SELinux settings and apply appropriate
    contexts to document roots, but if strange permissions errors persist,
    consider disabling SELinux (`sudo setenforce permissive`) while you
    troubleshoot.

Installation
------------

Installation is simple:

  1. Download or clone the script (damp) to a convenient location
  2. Make the script executable (`chmod u+x damp`)
  3. Run the setup command to install the necessary packages and configure the
     environment. Note that setup must be run as root. (`sudo ./damp setup`)

Other common setup options are:

  * `sudo ./damp setup --install`
    Installs all packages then places the damp script in /usr/bin and
    configuration files in /etc/damp.
  * `sudo ./damp setup -ium`
    Performs all actions in the list above plus installs a mail transport agent
    and [Drush][3]

Configuration
-------------

DAMP stores its configuration either in $HOME/.local/share/damp or in /etc/damp
if it has been installed. The configuration directory structure is as follows:

    /etc/damp
      -> damp.conf - global script configuration
      -> damp.pass - global passwords (ie. database root)
      -> sites
           -> mysite - directory containing configuration for 'mysite'
                -> conf - site-level options
                -> pass - site-level passwords

Options are stored in key=value pairs, one per line. There must be no spaces
around the equal sign and no other characters or comments. Options can be edited
by hand but the recommended method is to use the `damp set` command. When
installing the script to the system (`damp setup --install`), the configuration
tree from the current user's home directory will be copied to /etc/damp.

Examining damp.conf will reveal a number of default system paths. The script
will try to guess the correct paths based on your distribution, but you may need
to customize them if you have a non-standard setup.

Usage
-----

DAMP is primarily intended for configuring a web devlopment software stack and
then creating/managing Drupal sites. The script contains a number of commands
related to those tasks such as creating new sites, removing sites, and setting
global options.

The general syntax of a DAMP command is: `damp COMMAND [OPTION]... [SITE]`

### Commands ###

#### list
Shows a list of the installed sites that are managed by DAMP. There will be one
entry for every subdirectory in /etc/damp/sites. Each site will be listed
according to its name; for the full details including the domain, use the info
command. Can also be invoked using ls.

#### info
View details about a particular site including its FQDN, document root,
database, etc.. Info expects a site name, otherwise it will print a list of
sites. Use the --passwords option to display site passwords as well.

#### new
Creates a new Drupal site. Required options are:

  * name: The site's unique short name.
  * docroot: The full path to the Drupal directory for the site.

Optional options include:

  * domain: When combined with the site name, this will provide the full domain
    name of the site for use in URLs. (name.domain)
  * database: The database name to use for this site. Defaults to the site name.
  * make: Full path to a drush make file used to build the Drupal code base,
    which will be placed at the path specified in the docroot option. Drush must
    be installed and available in the PATH to use this option.

When it has all required options, DAMP will create a directory at
docroot/sites/name.domain conatining the Drupal settings file. It will also
create a database, Apache vhost configuration, and /etc/hosts entry. Once
complete, the script will attempt to open the Drupal installer in your default
web browser.

Once the Drupal installation in complete, you may want to consider running
`damp fixperms mynewsite` to correct post-install directory permissions.

#### open
Opens the specified site in your default web browser. If used with the --install
option, DAMP will open the site's install.php script. If used with the --dbadmin
option and the database administration package has been set up, DAMP will open
the database administration tool.

#### remove
Deletes a site from the system. This will remove the database, hosts entry
Apache configuration files, DAMP configuration directory, and the site
subdirectory from the Drupal document root.

#### set
Set site or global configuration options. Any options passed in on the command
line will be applied to the site if specified, or the global script
configuration if the site is omitted. DAMP will also alter the site's components
to match the new options. For example: if the site name is changed, directory
names will be altered, configuration files updated, etc.

#### get
Show a list of site/global cofiguration options. Use the --passwords option to
display passwords as well.

#### start
Start Apache and MySQL

#### stop
Stop Apache and MySQL

#### restart
Restart Apace and MySQL.

#### status
Show the current status Apache and MySQL

#### setup
Installs and configures the DAMP stack. With no options, setup will install any
necessary packages, configure SELinux if present, and writes out various conf
files. Options include:

  * install: Copies the script to /usr/bin/damp, making it available in the PATH
    and moves global configuration to /etc/damp.
  * drush: Installs the Drupal shell.
  * dbadmin: Installs phpMyAdmin for web-based database administration.
  * skip: Skips SELinux configuration; this operation can take a long time.

#### configure
edit configuration files

#### fixperms
Attempts to fix site directory permission issues. In general, it will make files
readable to all, set SELinux contexts and enable search permissions on all
directories in the document root's tree.

### Options ###

  -a, --dbadmin            install or open web-based database administration
                           front end
  -b, --database, --db     database name, defaults to site name
  -d, --domain             domain name (ie. example.com)
  -n, --name               site shortname (must be unique)
  -p, --docroot, --path    full path to the Drupal installation
  -M, --make               path to dush make file, will be built at docroot

  --dbuser                 database user name, either site user or root account
  --dbpass                 the database user password
              
  --dbconf                 mysql configuration file path
  --dbport                 mysql port number
  --httpdconf              apache configuration file path
  --httpdport              apache port number
  --httpdvhost             apache vhost configuration file directory
  --initpath               path to service init scripts
  --phpconf                php configuration file path

  -h, --help               display this help and exit
  -i, --install            install script to path or open site install page
                           used with setup or open
  --language               language to use during site installs
  -m, --mail               install system MTA, only used during setup
  --passwords              force display passwords in option lists
  -s, --skip               skip SELinux configuration during setup
  -u, --drush              install drush (the DRUpal SHell)
  --uninstall              uninstall script, configuration, and sites
                           used wuth setup
  -v, --version            output version information and exit
  -y, --yes                answer yes to all prompts

Examples
--------

    damp list
    damp open mysite
    damp set --domain example.com mysite
    damp new --name mysite -d example.com
    damp rm -y mysite
    damp configure php 


[1]: http://drupal.org/ "Drupal"
[2]: https://www.acquia.com/products-services/dev-desktop "Acquia Dev Desktop"
[3]: http://drupal.org/project/drush "Drush"
