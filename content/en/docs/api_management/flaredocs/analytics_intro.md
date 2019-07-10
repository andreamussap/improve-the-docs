---
title: "Introduction to API Gateway Analytics"

linkTitle: "Introduction to API Gateway Analytics"

date: 2019-7-5

description: > 
    This guide assumes that you have already installed API
    Gateway Analytics. For more details, see the [API Gateway Installation
    Guide](/bundle/APIGateway_77_InstallationGuide_allOS_en_HTML5/) .

---

﻿

API Gateway Analytics is a server runtime and web-based monitoring and
reporting console that enables you to generate scheduled reports and
analyze API use over time in multiple API Gateways across the domain.

![API Gateway
Analytics](../../Images/docbook/images/concepts/reporter.png "API Gateway Analytics"){.maxWidth}

API Gateway Analytics includes the following features:

-   Web-based console that monitors and reports on all API Gateways in
    the domain (multiple API Gateways are shown on the left in the
    diagram)
-   Reporting over an extended time period rather than immediate
    operational monitoring
-   Analysis of what APIs are used, how often APIs are used, when APIs
    are used, and who is using APIs
-   Scheduled reports in PDF format can be emailed to specific users

Install API Gateway Analytics
-----------------------------

This guide assumes that you have already installed API Gateway
Analytics. For more details, see the [API Gateway Installation
Guide](/bundle/APIGateway_77_InstallationGuide_allOS_en_HTML5/) .

For details on upgrading API Gateway Analytics from earlier versions,
see the [API Gateway Upgrade
Guide](/bundle/APIGateway_77_UpgradeGuide_allOS_en_HTML5) .

Configure your API Gateway Analytics metrics database
-----------------------------------------------------

Before starting API Gateway Analytics, you must perform the following
steps:

1.  Create the third-party JDBC database instance used to store metrics.
    For more details, see [Set up the metrics database for API Gateway
    Analytics](../CommonTopics/metrics_db_install.htm). Alternatively,
    if you already have an existing database, skip to the next step.
2.  Update your API Gateway Analytics configuration with the database
    details using the `configureserver` script. For more details, see
    [Configure API Gateway Analytics](analytics_config.htm).
3.  Configure the database tables using the `dbsetup` script. For more
    details, see [Set up the metrics database for API Gateway
    Analytics](../CommonTopics/metrics_db_install.htm).
4.  Configure your API Gateway instance and Admin Node Manager to store
    metrics. For more details, see [Configure API Gateway with the
    metrics database](../CommonTopics/metrics_gw_config.htm).

For more details on managing metrics, see [Purge the metrics database
for API Gateway Analytics](../CommonTopics/metrics_db_purge.htm).

Monitoring and reporting with API Gateway Analytics
---------------------------------------------------

When you have configured the metrics database and API Gateway Analytics,
you can start monitoring your API traffic and generating reports in API
Gateway Analytics.

For details, see the following:

-   [Launch API Gateway Analytics](analytics_start.htm)
-   [Monitor traffic with API Gateway
    Analytics](analytics_monitoring.htm)
-   [Configure scheduled reports in API Gateway AnalyticsScheduled
    report settings](analytics_scheduled_reports.htm)
