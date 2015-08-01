---
layout: post
title:  "Allowing remote connections to PostgreSQL 9.3"
date:   2015-07-01 23:27
categories: postgresql
---

Setting up PostgreSQL on a server will allow you to connect locally, but if you need to access the DB from a remote system or another server, you'll need to allow TCP/IP connections.

First we'll need to edit the config.


### Where is my config?

If you don't know where the config is located, PostgreSQL itself can tell you.  On the system where Postgres is installed, log into the psql console:

    psql -U postgres

Once logged into the Postgres console, run the following command:

    SHOW config_file;

Voila:

                   config_file
    ------------------------------------------
     /etc/postgresql/9.3/main/postgresql.conf
    (1 row)

You can escape the postgres console with **\q**.


### Edit postgresql.conf

(Replace _vim_ with _nano_, if you prefer)

    sudo vim /etc/postgresql/9.3/main/postgresql.conf

Search for mention of `listen_addresses`, which was around line 59 in my config.  Should look something like this:

    #listen_addresses = 'localhost'

Change it like follows.  NOTE: change the second IP address to match the IP of your server.

    listen_addresses = '127.0.0.1,192.168.0.1'

Save the file and exit.


### Edit pg_hba.conf

    sudo vim /etc/postgresql/9.3/main/pg_hba.conf

At the bottom of this file add the following line (again, replace the 192.168.0.1 address with your server IP)

    host    all     all     192.168.0.1    255.255.255.255 trust

Save and exit.

Now reboot postgres.

    sudo service postgresql restart


### Connecting

On the computer you're trying to connect from, try connecting:

    psql -h 192.168.0.1 -u postgres postgres

Note: If get a message saying an IP address isn't the pg_hba.conf, copy the address and replace the one in pg_hba.conf.
