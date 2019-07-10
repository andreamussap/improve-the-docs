---
title: "setup-cassandra script reference"
linkTitle: "setup-cassandra script"
weight: 7
date: 2019-06-05
description: >
  A reference to the `setup-cassandra` script.
---

<span id="Set"></span>The `setup-cassandra` script provided by API
Gateway enables you to configure a multi-node Cassandra HA cluster
automatically. You can use this script when Cassandra is installed
locally along with API Gateway, or installed remotely on a different
node. For details on supported Cassandra deployment architectures and HA
production environments, see [Configure a highly available Cassandra cluster](cassandra_config).

API Gateway provides the `setup-cassandra` script to help configure a
Cassandra cluster by updating your Cassandra configuration files and
providing instructions to finalize the configuration. This script also
creates an automatic backup of the original `cassandra.yaml` file in the
following format:

```
<timestamp>_cassandra.yaml.bak
```

This topic describes how to run the `setup-cassandra` script, and how to
configure a multi-node Cassandra cluster with username/password
authentication enabled. It also describes how to configure TLS v1.2
encryption for client-server and inter-node communications in the
cluster.

## Prerequisites

The following prerequisites apply for the `setup-cassandra` script:

### Python

  - Cassandra 2.2.5 requires Python 2.7.x *(up to 2.7.10)*
  - Cassandra 2.2.8 requires Python 2.7.x *(up to the latest)*
  - Cassandra 2.2.12 requires Python 2.7.x *(up to the latest)*

{{% alert title="Note" %}}
API Gateway 7.7 supports Cassandra 2.2.12. API Gateway 7.5.3 supports Cassandra versions 2.2.5 and 2.2.8.
{{% /alert %}}

### User name and password authentication

User name and password authentication for clients connecting to the
cluster is enabled by the `setup-cassandra` script by default. However,
you must change the default user name and password created by Cassandra
on startup to further secure the installation. The script provides
instructions describing how to change the default user name and password
and replicate the `system_auth` keyspace using the `cqlsh` command.

For more details, see [Secure Cassandra HA configuration](#Secure).

### <span id="Remote"></span>Remote Cassandra HA nodes

`setup-cassandra` is available by default when Cassandra is installed
locally on the same node as API Gateway. You can also configure remote
Cassandra nodes to use the `setup-cassandra` script supplied by the API
Gateway installation. Alternatively, you can perform all necessary
Cassandra configuration changes manually. For details, see [Configure a highly available Cassandra cluster](cassandra_config).

To configure a remote Cassandra node to use the `setup-cassandra`
script, perform the following steps:

1.  Ensure that the `JAVA_HOME` environment variable is set on the
    remote Cassandra node.
2.  Ensure that Python is installed and added to your `PATH` environment
    variable on the remote Cassandra node.
3.  Change to the following directory on the local API Gateway node:
    ```
    AXWAY_HOME/apigateway/system/lib/jython/
    ```
4.  Copy the `setup-cassandra.py` file to your chosen directory on the
    remote Cassandra node.

## Run the setup-cassandra script

The instructions for running the `setup-cassandra` script depend on
whether the node is local or remote to API Gateway.

### Run setup-cassandra on local API Gateway node

On Linux, the `setup-cassandra` script is bundled with the API Gateway
installation and located in the `bin` directory. To run this script on a
local node:

```
$ cd AXWAY_HOME/apigateway/posix/bin
$ ./setup-cassandra <options>
```

### <span id="Run"></span>Run setup-cassandra on remote Cassandra node

On a remote Cassandra node, you first must ensure that the
`setup-cassandra` script has been configured as described in [Remote Cassandra HA nodes](#Remote). To run this script on a remote node:

```
$ ./setup-cassandra.py <options>
```

{{% alert title="Note" %}}
The example commands in this topic assume that Cassandra is installed locally on an API Gateway node.
{{% /alert %}}

## <span id="Configur"></span>Configure the seed node

To configure Cassandra to run as the cluster seed node, run the
`setup-cassandra` script with the following options:

```
setup-cassandra --seed --own-ip=<OWN_IP> --nodes=<NUMBER_OF_NODES> --cassandra-config=<CONFIG_FILE>
```

These options are described as follows:

| Option     | Description                                                                                                                                                          |
| ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `OWN_IP`      | IP address of this Cassandra host. Cassandra uses this IP address for communicating with other nodes in the cluster and for receiving client connections. |
| `NODES`       | Total number of the nodes in the Cassandra cluster.                                                                                                       |
| `CONFIG_FILE` | Full path to `cassandra.yaml` configuration file. Typically the path is `<CASSANDRA_INSTALL_DIR>/conf/cassandra.yaml`.                                    |

For example:

```
setup-cassandra --seed --own-ip=ipA --nodes=3 --cassandra-config=/opt/cassandra/conf/cassandra.yaml
```

## Configure additional nodes

To configure Cassandra on the remaining cluster nodes, run the
`setup-cassandra` script with the following options:

```
setup-cassandra --seed-ip=<SEED_IP> --own-ip=<OWN_IP> --cassandra-config=<CONFIG_FILE>
```

These options are described as
follows:

| Option        | Description                                                                                                                                                          |
| ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `SEED_IP`     | IP address of the Cassandra seed host (see [Configure the seed node](#Configur)).                                                                         |
| `OWN_IP`      | IP address of this Cassandra host. Cassandra uses this IP address for communicating with other nodes in the cluster and for receiving client connections. |
| `CONFIG_FILE` | Full path to `cassandra.yaml` configuration file. Typically the path is `<CASSANDRA_INSTALL_DIR>/conf/cassandra.yaml`.                                    |

For example:

```
setup-cassandra --seed-ip=ipA --own-ip=ipB --cassandra-config=/opt/cassandra/conf/cassandra.yaml
```


## <span id="Secure"></span>Secure Cassandra HA configuration

This section explains how to use the `setup-cassandra` script to secure
your Cassandra HA configuration. The examples assume that Cassandra is
installed locally on the same host as API Gateway. See also [Run setup-cassandra on remote Cassandra node](#Run).

### Reset your default user name and password

You can use the `setup-cassandra` script to reset the default user name
and password (`cassandra`/`cassandra`). Run this command to see the
instructions that you need to follow. For example, on the seed node the
instructions are as follows:

{{< highlight bash "hl_lines=1 5-7 11-12" >}}
./setup-cassandra --seed --own-ip=ipA --nodes=3 --cassandra config=/opt/cassandra/conf/cassandra.yaml

Connect to Cassandra with cqlsh and run following commands to create an alternative superuser account:

CREATE USER admin WITH PASSWORD 'amujsa26al2ns' SUPERUSER;QUIT
PLEASE MAKE A NOTE OF USERNAME AND PASSWORD FOR THE NEW SUPERUSER ACCOUNT:
USERNAME: admin PASSWORD: amujsa26al2ns

Connect to Cassandra using newly created account to lock out the default Cassandra superuser account and update "system_auth" keyspace replication factor:

/opt/cassandra/bin/cqlsh -u admin -p amujsa26al2ns node1 ALTER USER cassandra WITH PASSWORD 'g5q5h4h3bf1pnh2nsra9iucd82d7f1jams468vhaiimtibtuqpf' NOSUPERUSER;
ALTER KEYSPACE "system_auth" WITH REPLICATION = { 'class': 'SimpleStrategy', 'replication_factor': 3 }; QUIT
{{< / highlight >}}


{{% alert title="Note" %}}
If you are setting up a Cassandra HA cluster, you must replicate the `system_auth` keyspace as shown in this example. This enables API Gateway to communicate with the cluster if a node goes down. For more details, see the [Configuring authentication](https://docs.datastax.com/en/archived/cassandra/2.2/cassandra/configuration/secureConfigNativeAuth.html?hl=authentication) documentation.
{{% /alert %}}

### Enable node-to-node traffic encryption

To configure TLS v1.2 encryption, you must generate a private key and
certificate for every Cassandra node in the cluster. You must also
obtain the certificate for the Certification Authority (CA) used to
generate the node certificate. You can use Policy Studio to create the
necessary certificates and keys or any other suitable method for
generating certificates.

You must export and save the node certificate and private key as a
PKCS12 file named `server.p12`, and save the CA certificate as a PEM
file named (`server-ca.pem`). You must place these files into the same
directory where you run the `setup-cassandra` script from.

To use the `setup-cassandra` script to configure node-to-node TLS/SSL
encryption, add the `--enable-server-encryption` option. For
example:

```
setup-cassandra --seed-ip=ipA --own-ip=ipB --cassandra-config=/opt/cassandra/conf/cassandra.yaml --enable-server-encryption
```

After you run the `setup-cassandra` script, it provides instructions for
converting the keys and certificates to a format required by Cassandra.
After you perform these instructions, you can remove the `server.p12`
and `server-ca.pem` files from the
system.

{{% alert title="Caution" color="warning" %}}
Anyone with a private key or certificate signed by `server-ca.pem` can connect to the cluster. You should limit the use of this CA to signing the node certificates only. In particular, do not use the same CA to sign client-to-node certificates.
{{% /alert %}}

### Enable client-to-node traffic encryption

The requirements for setting up client-to-node TLS/SSL encryption are
the same as the node-to-node TLS/SSL requirements. However, you must
save the CA certificate/private key and node certificate in
`client-ca.pem` and `client.p12` files respectively. For example, to
instruct the `setup-cassandra` script to configure client-to-node
encryption, add the `--enable-client-encryption` option to script
arguments:

```
setup-cassandra --seed-ip=ipA --own-ip=ipB --cassandra-config=/opt/cassandra/conf/cassandra.yaml --enable-client-encryption
```


After you run the `setup-cassandra` script, it provides instructions for
converting the keys and certificates to a format required by Cassandra.
After you perform these instructions, you can remove the `client.p12`
and `client-ca.pem` files from the system.

### Configure the cqlsh command for client-to-node traffic encryption

When the Cassandra cluster has been configured to use client-to-node
TLS/SSL encryption, you must configure all clients connecting to the
cluster (including `cqlsh`) to use TLS/SSL.

If client-to-node TLS/SSL encryption has been enabled, the
`setup-cassandra` script creates a configuration file (`cqlshrc`) with
the necessary configuration to enable TLS/SSL encryption. However, you
must provide following files to configure `cqlsh` for TLS/SSL:

  - `client-ca.pem`: PEM file containing CA certificate
  - `cqlsh-cert.pem`: PEM file containing client certificate for `cqlsh`
  - `cqlsh-key.pem`: PEM file containing private key of client certificate for `cqlsh`

## Updated Cassandra configuration

This section shows the updates that the `setup-cassandra` script makes
to the `cassandra.yaml` configuration file:

### General settings

  - `seed_provider`, `parameters`, `seeds`: <*IP address of seed node*>
  - `start_native_transport`: `true`
  - `native_transport_port`: `9042`
  - `listen_address`: IP address of the node
  - `rpc_address`: IP address of the node
  - `authenticator`: `org.apache.cassandra.auth.PasswordAuthenticator`
  - `authorizer`: `org.apache.cassandra.auth.CassandraAuthorizer`

### Node-to-node traffic encryption

If node-to-node TLS/SSL traffic encryption is enabled:

  - `server_encryption_options`: Specified options
  - `internode_encryption`: all
  - `keystore`: <`server-keystore.jks`>
  - `keystore_password`: Randomly generated password
  - `truststore`: <`server-truststore.jks`>
  - `truststore_password`: Randomly generated password
  - `protocol`: TLS
  - `store_type`: JKS
  - `algorithm`: SunX509
  - `require_client_auth`: true

### Client-to-node traffic encryption

If client-to-node TLS/SSL traffic encryption is enabled:

  - `client_encryption_options`: Specified options
  - `enabled`: true
  - `optional`: false
  - `keystore`: <`client-keystore.jks`>
  - `keystore_password`: Randomly generated password
  - `truststore`: <`client-truststore.jks`>
  - `truststore_password`: Randomly generated password
  - `protocol`: TLS
  - `store_type`: JKS
  - `algorithm`: SunX509
  - `require_client_auth`: true
