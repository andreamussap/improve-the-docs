--- 
title: "[]{#top}Configure connecting to multiple API Managers"
linkTitle: "[]{#top}Configure connecting to multiple API Managers" date:
2019-7-3 description: &gt; This topic describes how to configure
connecting API Portal to multiple API Managers and the prerequisites for
it. 
--- ﻿

This topic describes how to configure connecting API Portal to multiple
API Managers and the prerequisites for it.

-   [Configure synchronization policies for API Managers](#Configur)
-   [Configure API Portal](#Configur2)

Prerequisites
-------------

Before you start, you must have the following:

-   All the API Manager instances configured and running. .
-   The required organizations created on all API Managers. Ensure that
    the name of an organization is identical across all instances.
-   Users created and assigned to organizations on the *master* API
    Manager.
-   A Redis cache installed. The Redis cache is delivered alongside
    API Portal, and you can choose to install it while installing
    API Portal. For more details, see [Install
    API Portal](requirements.htm).

For more details on installing and configuring API Manager, see the [API
Gateway Installation
Guide](/bundle/APIGateway_77_InstallationGuide_allOS_en_HTML5/) and [API
Manager User Guide](/bundle/APIManager_77_APIMgmtGuide_allOS_en_HTML5/)
.

[]{#Configur}Configure synchronization policies for API Managers
----------------------------------------------------------------

The slave API Managers communicate with the master API Manager using an
external identity provider policy. A sample policy container and
policies are included in the installation package, and you can find it
`(#insert paths here)`{style="color: #ff0000;"}.

You must import the policy container to Policy Studio, configure the
sample policies to point to your master API Manager, configure your
environment settings, and deploy the configuration to your slave API
Manager. For more information on working in Policy Studio, see [API
Gateway Policy Developer
Guide](/bundle/APIGateway_77_PolicyDevGuide_allOS_en_HTML5/) .

{{&lt; alert title="Note" color="primary" &gt;}}You must create a
separate project for each slave API Manager to use separate connections
when deploying the configured policy.{{&lt; /alert &gt;}}

### Import and configure the policies

[(\#\#names subject to change)]{style="color: #ff0000;"}

1.  In Policy Studio, create a new project from the API Gateway instance
    running the *slave* API Manager you want to configure.
2.  Click **File &gt; Import &gt; Import Configuration Fragment**, and
    import `AuthoToMasterDynamicOrg.xml`.
3.  In the Policy Studio node tree, click **Policies &gt; Sample
    policies &gt; AuthoToMaster**, and open the policy **Authenticate to
    Master**.
4.  Double-click the **Login to Master** routing filter, and change the
    IP address in the URL to point to your *master* API Manager.
5.  On the **Settings** tab, under **Redirect**, ensure that **Follow
    redirects** is not selected and click **Finish**.
6.  Open the policy **Get Current Info**, and repeat steps 4 and 5 on
    the **get current info** routing filter.
7.  Open the policy **Get Organization Name**, and repeat steps 4 and 5
    on the **get organization name** routing filter.

### Configure environment settings

1.  In the Policy Studio node tree, click **Environment
    Configuration &gt; Listeners &gt; API Gateway &gt; API Portal &gt;
    Ports**.
2.  Open the configured port, go to the **Mutual Authentication** tab,
    and check that **Ignore client certificates** is selected.
3.  In the node tree, click **Environment Configuration &gt;
    Listeners &gt; API Gateway &gt; API Portal &gt; Paths**, and under
    `/api/portal/`, double-click **API Manager API v1.2**.
4.  Click **Add**, and add a new servlet property
    `CsrfProtectionFilterFactory.refererWhitelist` with the value of the
    URL of the *master*API Manager. If you already have this servlet
    property, you can edit it and add the *master*API Manager URL. If
    you list multiple URLs, separate them with a comma.
5.  Repeat the previous step on **API Manager API v1.3**.
6.  In the node tree, click **Server Settings &gt; General**, and ensure
    that **Server's SSL cert's name must match name of requested
    server** is not selected.
7.  Click **Server Settings &gt; API Manager &gt; Identity Provider**,
    select **Use external identity provider**.
8.  Set **Account authentication policy** to the **Authenticate to
    Master** policy and **Account information policy** to **Get Current
    Info**, and click **Save**.
9.  Deploy the configuration to API Gateway.

[]{#Configur2}Configure API Portal
----------------------------------

1.  Log in to the Joomla! Admin Interface (JAI)
    (`https://<API Portal host>/administrator`).
2.  Connect to your master API Manager normally, see [Connect API Portal
    to a single API Manager](link_portal_to_api_manager.htm).
3.  Click **Components &gt; API Portal &gt; Multiple API Managers**.
4.  Enter the public label (for example, `Environment`) for the API
    Managers. An API consumer sees the available API Managers listed
    under this title.
5.  In **Login Failure Policy**, select how failed logins are handled.
6.  Click the **+** button to add a slave API Manager, and enter a
    public name for it. This name is shown in the list of API Managers
    available to an API consumer.
7.  Enter the host IP address and port of the slave API Manager.
8.  Configure the rest of the settings if required, then click **Save**.
9.  Repeat to add the rest of your slave API Managers.
