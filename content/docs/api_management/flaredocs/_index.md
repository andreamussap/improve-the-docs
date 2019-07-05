---

title: "About this guide"

linkTitle: "About this guide"

date: 2019-7-5

description: > 
    This guide describes how to set up and how to use API
    Gateway Analytics. This tool enables you to record, monitor, and report
    on the history of message traffic between API Gateway instances and
    various services, remote hosts, and clients running in an API Gateway
    domain.

---

﻿

This guide describes how to set up and how to use API Gateway Analytics.
This tool enables you to record, monitor, and report on the history of
message traffic between API Gateway instances and various services,
remote hosts, and clients running in an API Gateway domain.

Who should read this guide
--------------------------

The intended audience for this guide includes system administrators,
database administrators, API Gateway administrators, and policy
developers.

How to use this guide
---------------------

This guide should be used in conjunction with the other guides in the
API Gateway documentation set. Before you begin, review this guide
thoroughly. The following is a brief description of the contents:

-   [Introduction to API Gateway Analytics](analytics_intro.htm) - Gives
    a brief overview of API Gateway Analytics and outlines the steps
    required to set up and use this tool.
-   [Set up the metrics database for API Gateway
    Analytics](../CommonTopics/metrics_db_install.htm) - Explains how to
    create and configure a database for monitoring in API Gateway
    Analytics.
-   [Configure API Gateway with the metrics
    database](../CommonTopics/metrics_gw_config.htm) - Explains how to
    configure an API Gateway instance and Node Manager to store metrics
    on historic traffic in the API Gateway Analytics metrics database.
-   [Configure API Gateway Analytics](analytics_config.htm) - Explains
    how to update your configuration (for example, port, database
    connection, and user credentials) before starting API Gateway
    Analytics.
-   [Launch API Gateway Analytics](analytics_start.htm) - Explains how
    to start API Gateway Analytics and how to change the default API
    Gateway Analytics user credentials and sample certificate.
-   [Monitor traffic with API Gateway
    Analytics](analytics_monitoring.htm): - Explains how to monitor your
    API Gateway domain and generate reports on historic API Gateway
    message traffic.
-   [Configure scheduled report
    settings](analytics_scheduled_reports.htm) - Explains how to
    schedule API Gateway Analytics reports to run on a regular basis,
    and to email the results to users in PDF format.
-   [Purge the metrics database](../CommonTopics/metrics_db_purge.htm) -
    Explains how to use the `dbpurger` command to purge old data and to
    archive data.
