apache
======

Installs and configures the Apache web server, including mod_ssl.

Role Variables
--------------

 * apache_User: apache
 * apache_Group: apache

 * apache_ServerAdmin: root@localhost
 * apache_ServerTokens: Prod

 * apache_Timeout: 60

 * apache_KeepAlive: 'Off'
 * apache_MaxKeepAliveRequests: 100
 * apache_KeepAliveTimeout: 15

 * apache_prefork_StartServers: 8
 * apache_prefork_MinSpareServers: 5
 * apache_prefork_MaxSpareServers: 20
 * apache_prefork_ServerLimit: 256
 * apache_prefork_MaxClients: 256
 * apache_prefork_MaxRequestsPerChild: 4000

    Each of the the previous variables are passed directly to the
    corresponding Apache setting, in the httpd.conf file. See Apache
    documentation for details.

 * apache_use_default_logging: yes
    When set to 'yes', Apache's default logging configuration will be left
    alone. Otherwise, the global logging configuration will be disabled. In
    that case, logging will have to be configured through other means,
    probably via an apache_conf_templates file.
 * apache_use_default_logrotate: yes
    When set to 'yes', the default logrotate configuration for Apache will be
    left alone. Otherwise, it will be removed, and log rotation will have to
    be handled by other means.

 * apache_sysconfig: []
    Specifies a list of lines to add to the httpd sysconfig file. This is
    particularly useful if the PATH environment variable needs to be
    configured.

 * apache_listen_port: 80
 * apache_listen_port_ssl: 443
    Specifies the appropriate ports to use for http and https traffic.

This module can create vhosts for you, configured via vars. It's rather
limited in functionality, but works for simple vhost configuration.

 * apache_create_vhosts: false
    Determines whether to configure vhosts or not.
 * apache_vhosts: []
 * apache_vhosts_ssl: []
    Specifies the vhost configuration. Each item may have the following keys:

        * servername            Required
        * documentroot          Required
        * serveralias           Optional
        * serveradmin           Optional
        * directoryindex        Optional
        * extra_parameters      Optional

If more control over vhost configuration is needed, this role can also
install a set of defined templates.

 * apache_conf_template_root: ''
    Specifies the path (absolute or relative) that contains the templates.
 * apache_conf_templates: []
    Specifies a list of templates to install. Each template listed here
    should be named as you'd like it installed. The template files themselves
    should be named with a '.j2' suffix.

This role can also manage htpasswd entries:

 * apache_use_htpasswd: false
    Determines whether to manage htpasswd file(s) or not.
 * apache_htpasswd_entries: []
    Specify the htpasswd entries; each item may have the following keys:
        * destfile              Required
        * username              Required
        * password              Required if state is 'present'
        * state                 Optional; defaults to 'present'.

Example Playbook
----------------

    # Create a simple vhost
    - hosts: servers
      vars:
        apache_create_vhosts: true
        apache_vhosts:
          - servername: www.website.com
            serveralias: website.com
            documentroot: /var/www/html
            serveradmin: webmaster@website.com
            directoryindex: index.html
      roles:
         - { role: apache }

    # Install vhost templates
    - hosts: servers
      vars:
        apache_conf_templates:
          - website.com.conf
          - website2.com.conf
      roles:
         - { role: apache }

