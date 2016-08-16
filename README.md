configuration management for MariaDB Galera cluster
===================================================

These roles allow you to automatically setup a MariaDB Galera cluster with sane
default settings.

These roles are currently only tested for RHEL/CentOS 7, but most tasks can be
reused for Debian or SUSE based distributions.

Prerequisites
-------------

The machine from which the playbook is being run needs to have Ansible >= 2.0
installed. For detailed information how to obtain current packages for your
distribution of choice have a look at the
[Ansible documentation](https://docs.ansible.com/ansible/intro_installation.html).

As we're accessing information from a group of hosts within these playbooks we
need to have fact caching enabled in Ansible. To, for example, cache using json
files in you homedirectory you would add the following lines to
``/etc/ansible/ansible.cfg``:

```
[defaults]
gathering = smart
fact_caching = jsonfile
fact_caching_connection = ~/.ansible/cache
```

For more information about other cache mechanisms have a look at the
[Ansible documentation](https://docs.ansible.com/ansible/playbooks_variables.html#fact-caching)
regarding fact-caching.

Installing requirements
-----------------------

To install the required packages and configure SELinux and the firewall you can
run only the tasks tagged with ``setup``

    ansible-playbook -i galera.hosts galera.yml --tags setup

Configure MariaDB Galera cluster
--------------------------------

To run all further tasks to configure MariaDB Galera cluster and add the
required user for the **S**tate **S**napshot **T**ransfer (SST) either skip the
tasks tagged ``setup`` or run the tags ``config`` or ``auth`` directly.

    ansible-playbook -i galera.hosts galera.yml --skip-tags setup

Bootstrapping MariaDB Galera cluster
------------------------------------

Bootstrapping the cluster can be done using a playbook dedicated for
bootstrapping the MariaDB Galera cluster called galera_bootstrap.yml

    ansible-playbook -i galera.hosts galera_bootstrap.yml

It uses the first node in the group ``galera_cluster`` to bootstrap the cluster.

Rolling MariaDB Galera cluster updates
--------------------------------------

The role ``galera_conf`` is set up to allow rolling updates of the configuration
of the Galera cluster. When the variable ``bootstrapped`` is set to ``yes`` the
``mariadb`` service is restarted after a change of the configuration. It is
important to note that the playbook contains the keyword ``serial: 1``, meaning
that the configuration is applied one node at a time so that the will never lose
quorum in the process of applying the new configuration.

    ansible-playbook -i galera.hosts galera_rolling_update.yml
