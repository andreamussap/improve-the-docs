---
title: "Manage Apache Cassandra"
linkTitle: "Manage Apache Cassandra"
weight: 4
date: 2019-06-05
description: >
  Start or stop Cassandra manually or as a service.
---
On Linux, when you select to install Cassandra using the API Gateway
installer as part of a Standard or Complete setup, Cassandra starts
automatically. To start or stop Cassandra manually or as a service,
perform the steps described in this
topic.

{{% alert title="Note" %}}
Before you start Cassandra, you must follow the best practices in [Apache Cassandra Best Practices](cassandra_bestpractices) to achieve a stable Cassandra environment, and to prevent data integrity and performance issues.
{{% /alert %}}

## <span id="Manage"></span>Manage Apache Cassandra

This section explains how to start and stop Cassandra.

### Start Apache Cassandra

To start Cassandra in the background:

1.  Open a command prompt, and change to the following directory:

```
$ cd CASSANDRA_HOME/bin
```

1.  Run the following command:

```
$ ./cassandra
```

To start Cassandra in the foreground, run the following command:

```
$ ./cassandra -f
```

For more details, see <https://wiki.apache.org/cassandra/RunningCassandra>.

### Start Cassandra as a service

To install Cassandra as a service, you must install and configure
appropriate startup script for your system. For example, see the
following example startup
    scripts:

  - **CentOS**:
  - [https://support.axway.com/kb/178063/language/en](https://support.axway.com/kb/178063/language/en "https://support.axway.com/kb/178063/language/en")
  - **Debian**:
  - [https://github.com/apache/cassandra/blob/cassandra-2.2/debian/init](https://github.com/apache/cassandra/blob/cassandra-2.2/debian/init "https://github.com/apache/cassandra/blob/cassandra-2.2/debian/init")

When startup scripts are configured, you can then start Cassandra as a
service.
{{% alert title="Note" %}}
You must have root or sudo permissions to start Cassandra as a service.
{{% /alert %}}

For example, typically the command to start Cassandra as a service is as
follows:

```
$ sudo service cassandra start
```

### Stop Cassandra

1.  Find the Cassandra Java process ID (PID):
```
$ ps auwx | grep cassandra
```

1.  Run the following command:
```
$ sudo kill *pid*
```

### Stop Cassandra as a service

You must have `root` or `sudo` permissions to stop the Cassandra service
as follows:
```
$ sudo service cassandra stop
```

## Connect to API Gateway for the first time

Connecting to API Gateway depends on your operating system and
installation setup type (Standard, Complete, or Custom).

### Standard or Complete setup

If you installed a default Standard or Complete setup (both include the
QuickStart tutorial), Cassandra is installed on the same host, and
listens on `localhost` by default. API Gateway runs on the same host and
connects to Cassandra by default.

### Custom setup

If you installed a Custom setup and did not select the Quickstart
tutorial, you must update the API Gateway Cassandra client configuration
as follows:

1.  Open the API Gateway group configuration in Policy Studio.
2.  Select **Server Settings \> Cassandra \> Authentication**, and set
    the Cassandra username/password (default is
    `cassandra`/`cassandra`).
3.  Select **Server Settings \> Cassandra \> Hosts**. Add an address for
    the Cassandra node. You can enter an IP address or host name.
4.  Deploy the configuration to the API Gateway group.

For more details on configuring **Server Settings** in the Policy Studio
client, see [Cassandra
Settings](/csh?context=105&product=prod-api-gateway-77) in the [API
Gateway Administrator
Guide](/bundle/APIGateway_77_AdministratorGuide_allOS_en_HTML5/). For
details on updating the Cassandra server configuration, see [Configure a highly available Cassandra cluster](cassandra_config).

## Further details

For more details on Apache Cassandra, see the
    following:

  - <http://cassandra.apache.org/>
  - [http://docs.datastax.com/en/cassandra/2.2/](http://docs.datastax.com/en/cassandra/2.2/ "http://docs.datastax.com/en/cassandra/2.2/")
