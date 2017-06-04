# Ansible Role: PHP

Installs PHP on RedHat/CentOS and Debian/Ubuntu (Ubuntu14.04, Ubuntu16.04) servers.

## Requirements

Must be running a separate web server, such as Nginx or Apache.

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

    php_packages: []

A list of the PHP packages to install (OS-specific by default). You'll likely want to install common packages like `php`, `php-cli`, `php-devel` and `php-pdo`, and you can add in whatever other packages you'd like (for example, `php-gd` for image manipulation, or `php-ldap` if you need to connect to an LDAP server for authentication).

    php_enable_webserver: true

If your usage of PHP is tied to a web server (e.g. Apache or Nginx), leave this default value. If you are using PHP server-side or to run some small application, set this value to `false` so this role doesn't attempt to interact with a web server.

    php_webserver_daemon: "httpd"

The default values for the HTTP server deamon are `httpd` (used by Apache) for RedHat/CentOS, or `apache2` (also used by Apache) for Debian/Ubuntu. If you are running another webserver (for example, `nginx`), change this value to the name of the daemon under which the webserver runs.

    php_enablerepo: ""

(RedHat/CentOS only) If you have enabled any additional repositories (might I suggest geerlingguy.repo-epel or geerlingguy.repo-remi), those repositories can be listed under this variable (e.g. `remi,epel`). This can be handy, as an example, if you want to install the latest version of PHP 5.4, which is in the Remi repository.

    php_executable: "php"

The executable to run when calling PHP from the command line. You should only change this if running `php` on your server doesn't target the correct executable, or if you're using software collections on RHEL/CentOS and need to target a different version of PHP.

### PHP-FPM

PHP-FPM is a simple and robust FastCGI Process Manager for PHP. It can dramatically ease scaling of PHP apps and is the normal way of running PHP-based sites and apps when using a webserver like Nginx (though it can be used with other webservers just as easily).

When using this role with PHP running as `php-fpm` instead of as a process inside a webserver (e.g. Apache's `mod_php`), you need to set the following variable to `true`:

    php_enable_php_fpm: false

You will also need to override the default `php_packages` list and add `php-fpm` (RedHat/CentOS) or `php5-fpm` (Debian/Ubuntu) to the list.

This role does not manage fpm-specific www pool configuration (found in `/etc/php-fpm.d/www.conf` on RedHat/CentOS and `/etc/php5/fpm/pool.d/www.conf` on Debian/Ubuntu), but rather allows you to manage those files on your own. If you change that file, remember to notify the `restart php-fpm` handler so PHP picks up the new settings once in place. Settings like `pm.max_children` and other `pm.*` settings can have a dramatic impact on server performance, and should be tuned specifically for each application and server configuration.


## Dependencies

None.

## Example Playbook

```
---
# Essential Task, need to run on all node
- hosts: icingalab
  remote_user: root
  roles:
    - { role: adduser, users: ["tungnt", "thaobtp"]  }

- hosts: icingalab
  remote_user: tungnt
  sudo: yes
  vars:
    php_enable_webserver: yes
    php_webserver_daemon: "apache2"
    php_executable: "php"
    php_enable_php_fpm: false
    php_use_managed_ini: false
  roles:
      - common
      - php
```

## License

MIT / BSD

## Author Information

This role was created in 2014 by [Jeff Geerling](http://jeffgeerling.com/), author of [Ansible for DevOps](http://ansiblefordevops.com/).
