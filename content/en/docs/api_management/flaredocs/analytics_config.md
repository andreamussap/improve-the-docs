---

title: "Configure API Gateway Analytics"

linkTitle: "Configure API Gateway Analytics"

date: 2019-7-5

description: > 
  This topic describes how to update an API Gateway
  Analytics configuration (for example, the API Gateway Analytics port,
  database connection, and user credentials) before starting API Gateway
  Analytics. You can use the `configureserver` script (recommended) to
  guide you through all the required steps, or you can use Policy Studio
  to configure the API Gateway Analytics configuration file.

---

﻿

This topic describes how to update an API Gateway Analytics
configuration (for example, the API Gateway Analytics port, database
connection, and user credentials) before starting API Gateway Analytics.
You can use the `configureserver` script (recommended) to guide you
through all the required steps, or you can use Policy Studio to
configure the API Gateway Analytics configuration file.

Prerequisites
-------------

The prerequisites for configuring API Gateway Analytics are as follows:

### Install API Gateway {#install-api-gateway condition="sw"}

Because API Gateway Analytics reports on transactions processed by API
Gateway in real time, you must ensure that API Gateway is installed. For
more details, see the [API Gateway Installation
Guide](/bundle/APIGateway_77_InstallationGuide_allOS_en_HTML5/) .

To view API Gateway metrics in API Gateway Analytics, you must also
enable the recording of metrics. For more details, see [Configure API
Gateway with the metrics
database](../CommonTopics/metrics_gw_config.htm#Enable).

### Install API Gateway Analytics

You must ensure that API Gateway Analytics is installed. For more
details, see the [API Gateway Installation
Guide](/bundle/APIGateway_77_InstallationGuide_allOS_en_HTML5/) .

### Configure a database

You must ensure that a JDBC-compliant database is installed to store the
API Gateway monitoring and transaction data. For more details, see [Set
up the metrics database for API Gateway
Analytics](../CommonTopics/metrics_db_install.htm).

Update API Gateway Analytics configuration
------------------------------------------

By default, API Gateway Analytics is configured to read message metrics
from a MySQL database stored on the local machine. You can use the
`configureserver` command to configure API Gateway Analytics to use an
alternative database, change the user credentials on the default
database connection, or use a different listening port.

### Update configuration on the command line

Perform the following steps to run `configureserver` in interactive
mode:

1.  Change to the following directory:

  -----------------------------------
  `INSTALL_DIR/analytics/posix/bin`
  -----------------------------------

**Linux**

`INSTALL_DIR/analytics/posix/bin`

**Windows**

`INSTALL_DIR\analytics\Win32\bin`

1.  Run the `configureserver` command.
2.  Enter the port on which the API Gateway Analytics server will
    listen. Defaults to `8040`. If you have another process already
    using this port on the machine on which API Gateway Analytics is
    installed, configure API Gateway Analytics to listen on a different
    port.
3.  Enter the database connection URL. Defaults to
    `jdbc:mysql://127.0.0.1:3306/reports`.
4.  The following table lists examples of connection URLs for the
    supported databases, where `reports` is the name of the database and
    `DB_HOST` is the IP address or host name of the machine on which the
    database is running:
5.  Enter the database user name. Defaults to `root`.
6.  Enter the database password.
7.  Enter whether API Gateway Analytics generates PDF-based reports.
    Defaults to `N`, which means that PDF reports are not generated.
    When set to `Y`, API Gateway Analytics generates PDF reports that
    include the same metrics displayed in the API Gateway Analytics
    window (for example, number of client requests, requests per
    service, and so on). For more details on generated PDF reports, see
    [Configure scheduled reports in API Gateway AnalyticsScheduled
    report settings](analytics_scheduled_reports.htm).
8.  Enter the user name to connect to the API Gateway Analytics process
    that generates PDF reports. Defaults to an administrator user.
9.  Enter the password to connect to the API Gateway Analytics process
    that generates PDF reports.
10. Enter the directory to which generated PDF reports are output (for
    example, `c:\reports`).
11. Enter whether to send generated PDF reports to email recipients. You
    will require an SMTP account with which to send the reports.
    Defaults to `N`.

The following command shows some example output in interactive mode:

+-----------------------------------------------------------------------+
| ``` {space="preserve"}                                                |
| /opt/Axway-7.7/analytics/posix/bin>configureserver                    |
| Connecting to configuration at : federated:file:////opt/Axway-7.7/ana |
| lytics/conf/fed/                                                      |
| configs.xml                                                           |
|                                                                       |
| Listening port [8040]:                                                |
| Configuring Database: Default Database Connection                     |
| Database URL [jdbc:mysql://127.0.0.1:3306/reports]:                   |
| Database user name [root]:                                            |
| Database password []: *****                                           |
| Enable report generation (Y, N) [N]: y                                |
| Report generation process connects as user name [admin]:              |
| Report generation process connects using password []: ********        |
| Report output directory []: c:\reports                                |
| Email reports (Y, N) [N]: y                                           |
| Default email recipient []: joe@example.com                           |
| Email from []: apigateway@axway.com                                   |
| Choose SMTP connection type:                                          |
|     0) None                                                           |
|     1) SSL                                                            |
|     2) TLS/SSL                                                        |
| Choice [0]:                                                           |
| SMTP host []: localhost                                               |
| SMTP port [25]:                                                       |
| SMTP user name []: jbloggs                                            |
| SMTP password []: *********                                           |
| Delete report file after emailing (Y, N) [Y]:                         |
| Press enter to exit...                                                |
| ```                                                                   |
+-----------------------------------------------------------------------+

### Update configuration using command-line options

You can also run the `configureserver` command with various options
(`--port`, `--dburl`, `--emailfrom`, `--emailto`, `--smtphost`, and so
on). For example, the following command configures the database
connection without emailing reports:

  --------------------------------------------------------------
  ``` {space="preserve"}
  configureserver --dburl=jdbc:mysql://127.0.0.1:3306/reports 
  --dbuser=root --dbpass=changeme --no-email
  ```
  --------------------------------------------------------------

The following command specifies to email reports and the associated SMTP
settings:

  -------------------------------------------------------------------------
      configureserver --dburl=jdbc:mysql://127.0.0.1:3306/reports 
      --dbuser=root --dbpass=changeme 
      –-email --emailto=joe@example.com --emailfrom=apigateway@axway.com 
      --smtptype=NONE --smtphost=192.168.0.174 --smtpport=25 
      --smtpuser=jbloggs --smtppass=changeme 
      --generate --gpass=changeme --gtemp=c:\reports
  -------------------------------------------------------------------------

For descriptions of all available options, enter the
`configureserver --help` command.

The `configureserver` script does not permit the euro character (`€`)
when specifying username and password options. However, you can specify
the pound (`£`) and dollar (`$`) characters instead.

### Update configuration in Policy Studio

The recommended way to configure API Gateway Analytics is using the
`configureserver` command, which guides you through the required
settings. However, you can also use the Policy Studio to configure
specific settings in your API Gateway Analytics configuration file. For
example, to configure the `reports` database, perform the following
steps:

1.  In your Policy Studio installation directory, run the `policystudio`
    command.

<!-- -->

1.  When Policy Studio starts up, select **File &gt; New Project**.
2.  In the New Project dialog, enter a name for the project and click
    **Next**.
3.  Select **From existing configuration** and click **Next**.
4.  Browse to the directory containing the API Gateway Analytics
    configuration file (`configs.xml`), for example:
5.  Select **Environment Configuration &gt; External Connections** in
    the Policy Studio tree, and expand the **Database Connections** tree
    node.
6.  Right-click the **Default Database Connection** tree node, and
    select **Edit**.
7.  The Database Connection dialog enables you to configure the database
    connection details. By default, the connection is configured to read
    metrics data from the `reports` database. Edit the details for the
    **Default Database Connection** on this dialog.
8.  For example, you should enter a non-default database user name and
    password. To connect to a database other than the default local
    database, right-click **Database Connections** in the tree, and
    select **Add a Database Connection**. For more details, see the [API
    Gateway Policy Developer
    Guide](/bundle/APIGateway_77_PolicyDevGuide_allOS_en_HTML5/) .

[]{#Enable}Ensure that metrics have been enabled for your API Gateway host
--------------------------------------------------------------------------

You must use the `managedomain` tool to enable writing of metrics from
the API Gateway instances on your host to the metrics database. This
enables the Node Manager to process event logs from your API Gateway
instances, and to write metrics data to the metrics database.

The following example uses the interactive `managedomain --menu`
command:

  --------------------------------------------------------------------------------------------------
  ``` {space="preserve"}
  Select option: 2
  Select a host:
  1) LinuxMint01
  2) Enter host name
  Enter selection from 1-2 [2]: 1
  Hit enter to continue...
  Enter a new host name [LinuxMint01]:
  Enter a new Node Manager name [Node Manager on LinuxMint01]:
  Enter a new Node Manager port [8090]:
  There is only one Node Manager in this domain so it must remain as an Admin Node Manager
  Do you want to create an init.d script for this Node Manager [n]:
  Do you want to reset the passphrase for the Node Manager on this host ? [n]:
  Do you wish to edit metrics configuration (y or n) ? [n]: y
  Do you wish to enable metrics (y or n) ? [y]: y
  Enter metrics database URL [jdbc:mysql://127.0.0.1:3306/reports]:
  Enter metrics database username [root]:
  Enter metrics database plaintext password [*******]:
  Testing Database connectivity for : jdbc:mysql://127.0.0.1:3306/reports, user : root
  Metrics database connectivity succeeded
  Metrics generation enabled. All other specified metrics settings updated.
  Metrics settings updated successfully. Please reboot Node Manager on completion of this program.
  Completed successfully.
  ```
  --------------------------------------------------------------------------------------------------

For more details, see [Configure API Gateway with the metrics
database](../CommonTopics/metrics_gw_config.htm).
