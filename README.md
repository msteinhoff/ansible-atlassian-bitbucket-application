# ansible-atlassian-bitbucket-application

Ansible role which installs and configures an Atlassian Bitbucket Server instance.

# Requirements

## Oracle Java JDK

This role requires the Java JDK to be installed. The exact path can be
configured, see below for variables.

You can use `ansible-java-oracle-jdk` for this.

## Oracle Java default keystore

When using self signed certificates, it may be required to add those as trusted
certificates in the default keystore.

You can use `ansible-java-keystore` for this.

## RDBMS

A relational database is required for Bitbucket, e.g. MySQL, Postgres or
Oracle.

You can use `ansible-mysql-server` for for this.

## JDBC driver

Bitbucket needs a JDBC driver to connect to the database, which may not be
provided by the Bitbucket distribution. The exact path must be configured.

You can use `ansible-mysql-jdbc-driver` to install the MySQL JDBC driver.

## Packetfilter

It may be need to configure packetilter rules for incoming or outgoing network
connections.  

# Dependencies

## ansible-yum-ius-repo

This role installs git2u from the [IUS](https://ius.io/) repository and uses the
`ansible-yum-ius-repo` role to make sure the yum repo is installed.

# Role Variables

Available variables are listed below, along with default values (see
`defaults/main.yml`).  

All variables have set sensible defaults and usually should not need any
configuration, The only variable required to be set is the path to the installed
JDK.  

## General settings

   bitbucketuser_name: atlbitbucket

User name under which the application will be installed. The account will be
created if it does not exist.  

    bitbucket_group_name: atlbitbucket

Group name under which the application will be installed. The group will be
created if it does not exist.

    bitbucket_service_name: atlbitbucket

Service name of the application.

    bitbucket_provide_root_environment: false

Provide preconfigured aliases to the root shell for easier navigation to
Bitbucket-specific directories.

    bitbucket_start_on_boot :false

Start Bitbucket automatically at boot.

Set this to true in production environments. Even better, use a process
supervisor e.g. systemd or monit.

## Installation variables

    bitbucket_perform_installation: false

Install or upgrade Atlassian Bitbucket automatically.

Upgrade is the same as installation, but the previously installed version
will not be removed. If the configured Bitbucket version does not match the
current Bitbucket version on the host, the configured Bitbucket version
will be installed.

This flag can be used as an additional safety mechanism so the Bitbucket
installation is not accidentally changed. It could e.g. be enabled only in
a special, separate playbook for installations.

    bitbucket_start_after_installation: false

Start Bitbucket instance after the role has finished an installation or an
upgrade.

This is handy if you want to restore a backup before the instance is started.

    bitbucket_installation_archive_url: https://downloads.atlassian.com/software/stash/downloads/atlassian-bitbucket-4.12.1.tar.gz

HTTP URL where to get the tar.gz archive for the Bitbucket application.

Depending on the environment, a HTTP proxy configuration may be needed in
ansible.

    bitbucket_installation_archive_checksum: bitbucket_installation_archive_checksum: sha256:8ea5f0e145c4bcbec9879c8ac2d124329d000b9c4b74ce78408b0629a9ef1664

Checksum of the tar.gz archive for the given version.

    bitbucket_installation_archive_name: atlassian-bitbucket-4.12.1

Folder name of the extracted Bitbucket instance.

    bitbucket_installation_software_directory: /opt/atlassian/bitbucket

Path to a directory where the Bitbucket instance should be installed.

    bitbucket_installation_jdbc_driver_jar_source_path: (empty)

Optional path to a JDBC driver jar file on the control machine that will be
copied into the Bitbucket instance lib directory on the remote machine.

## Configuration variables

    bitbucket_config_jre_directory: ''

Required path to the directory that contains the Oracle JDK 1.8.

    bitbucket_config_home_directory: /var/atlassian/application-data/bitbucket

Path to the Bitbucket home directory.

    bitbucket_config_jvm_heap_size: 1024m

How much memory the JVM should use.

This is passed to the `-xXs` and `-Xmx` JVM options.

    bitbucket_config_jvm_opts: ''

Additional JVM configuration options.

    bitbucket_config_tomcat_context_path: ''

The tomcat context path for the Bitbucket instance. By default,  Bitbucket is
reachable at /.

    bitbucket_config_tomcat_connectors:
      standalone:
        enabled: true
        type: standalone
        listening_port: 8090
      reverse_proxy:
        enabled: false
        type: proxy
        listening_port: 8091
        proxied_hostname: bitbucket.example.org
        proxied_port: 443

Dictionary with tomcat connector configurations. The dictionary can contain an
arbitrary number of connectors with arbitrary names.

# Example Playbook

    - hosts: bitbucket-servers
      roles:
         - role: ansible-atlassian-bitbucket-application
           bitbucket_config_jre_directory: '/usr/java/jdk1.8.0_72'

# License

MPLv2
