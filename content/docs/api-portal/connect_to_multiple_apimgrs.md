--- title: "Connect API Portal to multiple API Managers" linkTitle:
"Connect API Portal to multiple API Managers" date: 2019-7-3
description: &gt; This topic describes how to configure API Portal to
connect to more than one API Manager. --- ﻿

This topic describes how to configure API Portal to connect to more than
one API Manager.

[]{#Example}Example architectures
---------------------------------

APIs are often hosted in several API Manager instances, for example, in
multiple environments (sandbox, testing, production) or in multiple API
Catalogs (for example, for internal users and for external users).
Instead of linking API Portal to just one API Manager, you can configure
API Portal to connect to multiple API Managers so that the exposed APIs
can be explored centrally in one place.

For example, you can configure API Portal as a centralized catalog
presenting API across all environments:

![Illustration on how API Portal centralizes APIs from multiple
environments](../Resources/Images/APIPortal/API_Portal_multiMgr_environment.png){.maxWidth}

This type of portal is recommended only for internal purposes and
internal users.

Alternatively, API Portal can pull APIs from different deployments. For
example, you could host one API Manager with non-business critical APIs
in a cloud, and another API Manager containing the APIs on confidential
data on-premise. A single API Portal can connect to both deployments,
and expose the APIs as configured in each API Manager:

![Illustration showing API Portal exposing APIs from both cloud and
on-premise
deployments.](../Resources/Images/APIPortal/API_Portal_multiMgr_cloud.png){.maxWidth}

[]{#Data}Data synchronization
-----------------------------

API Managers and API Catalogs are configured normally, each API Manager
controlling how the APIs in it are exposed.

One of the API Managers is assigned as a master API Manager, and it
controls all user operations, such as signing up and signing in to
API Portal, forgotten password, and updates to user profiles. The rest
of the API Managers act as slaves that automatically upon user login
call the master API Manager using a specific policy for the user
information, and then replicate the user and create API Portal user
session cookies.

Other operations in API Portal, such as managing organizations, APIs,
and applications, are done separately for each API Manager as usual.

For better performance, you can configure API Portal to cache the APIs
from all API Managers in a Redis cache. Instead of directly querying
each API Manager separately, API Portal reads the APIs from the cache.
This improves the performance, especially when there are hundreds of
APIs.

### Example login flow with multiple API Managers

1.  A user tries to log in to API Portal.
2.  API Portal sends a login request including the login credentials to
    all API Managers.
3.  API Manager finds the user in its user registry, and allows the user
    to log in.
4.  If a slave API Manager does not find the user, it triggers a
    specific policy that calls to the master API Manager to query for
    the user.
5.  If the master API Manager finds the user, it returns all the user
    parameters (name, email, organization) to the slave API Manager.
6.  The slave API Manager creates a session cookie for the user and
    returns that to API Portal along with the user's organization and
    APIs and applications in it.
7.  The user is logged in to API Portal .
8.  When the user is logged out, the session is destroyed on the slave
    API Managers.

After a successful login, the user can see APIs and applications
assigned to the user's organization in all API Manager instances. For
example, the user A belongs to organization X that has the following
configurations on two API Managers:

  -------------------------------------------------------------------------------------------------------------------------------------------------------------
  Master API Manager                                                             Slave API Manager
  ------------------------------------------------------------------------------ ------------------------------------------------------------------------------
  -   Organization X has application F that is shared with the user A.           -   Organization X has application Q that is shared with the user A.
  -   Organization X has APIs C and D that are both assigned to application F.   -   Organization X has APIs G and H that are both assigned to application Q.
  -------------------------------------------------------------------------------------------------------------------------------------------------------------

This means that when API Portal is connected to both API Managers, the
user A sees the APIs C, D, G, and H listed in API Catalog, and
applications F and Q on the Applications page. If API Portal was only
connected to, say, master API Manager, the user A would see only APIs C
and D, and application F.

[]{#Prerequi}Prerequisites
--------------------------

Before you can connect API Portal to multiple API Managers, ensure that:

-   All the API Manager instances are configured and running.
-   All the organizations are created on *all* API Managers. Ensure that
    the name of an organization is identical across all instances.
-   Users are created and assigned to organizations on the *master* API
    Manager.

For more details on installing and configuring API Manager, see the [API
Gateway Installation
Guide](/bundle/APIGateway_77_InstallationGuide_allOS_en_HTML5/) and the
[API Manager User
Guide](/bundle/APIManager_77_APIMgmtGuide_allOS_en_HTML5/) .

[]{#Configur}Configure synchronization policies for API Managers
----------------------------------------------------------------

The slave API Managers communicate with the master API Manager using an
external identity provider policy. A sample policy container
`AuthoToMasterDynamicOrg.xml` is included in the API Portal installation
package available from Axway Support at
[https://support.axway.com](https://support.axway.com/){.hyperlink}.

You must import the policy container to Policy Studio, configure the
sample policies to point to your master API Manager, configure your
environment settings, and deploy the configuration to your slave API
Manager. For more information on working in Policy Studio, see the [API
Gateway Policy Developer
Guide](/bundle/APIGateway_77_PolicyDevGuide_allOS_en_HTML5/) .

{{&lt; alert title="Note" color="primary" &gt;}}You must create a
separate project for each slave API Manager to use separate connections
when deploying the configured policy.{{&lt; /alert &gt;}}

### Import and configure the policies

1.  In Policy Studio, create a new project from the API Gateway instance
    running the *slave* API Manager you want to configure.
2.  Click **File &gt; Import &gt; Import Configuration Fragment**, and
    import `AuthoToMasterDynamicOrg.xml`.
3.  In the Policy Studio node tree, click **Policies &gt; Sample
    policies &gt; AuthoToMaster**, and open the policy
    **AuthenticateToMaster**.
4.  Double-click the **Login to Master** routing filter, and change the
    IP address and port in **URL** to point to your *master* API
    Manager.
5.  On the **Settings** tab, under **Redirect**, ensure that **Follow
    redirects** is not selected and click **Finish**.
6.  Open the policy **Get Current Info**, and repeat steps 4 and 5 on
    the **get current info** routing filter.
7.  Open the policy **GetOrganizationName**, and repeat steps 4 and 5 on
    the **get organization name** routing filter.

### Configure environment settings

1.  In the Policy Studio node tree, click **Environment
    Configuration &gt; Listeners &gt; API Gateway &gt; API Portal &gt;
    Ports**.
2.  Open the configured port, go to the **Mutual Authentication** tab,
    and check that **Ignore client certificates** is selected.
3.  In the node tree, click **Environment Configuration &gt;
    Listeners &gt; API Gateway &gt; API Portal &gt; Paths**, and under
    `/api/portal/`, double-click **API Manager API v1.2**, and click
    **Add**.
4.  Add a new servlet property:
5.  -   **Name**: `CsrfProtectionFilterFactory.refererWhitelist`
    -   **Value**: the login URL of the *master*API Manager (for
        example, `https://10.142.10.4:8075/api/portal/v1.3/login`)

6.  If you already have this servlet property, you can simply edit the
    value. If you list multiple URLs, separate them with a comma.
7.  Repeat the previous step on **API Manager API v1.3**.
8.  In the node tree, click **Server Settings &gt; General**, and ensure
    that **Server's SSL cert's name must match name of requested
    server** is not selected.
9.  Click **Server Settings &gt; API Manager &gt; Identity Provider**,
    select **Use external identity provider**.
10. Set **Account authentication policy** to the
    **AuthenticateToMaster** policy and **Account information policy**
    to **Get Current Info**, and click **Save**.
11. Deploy the configuration to API Gateway.

[]{#Configur2}Configure the connection to slave API Managers
------------------------------------------------------------

1.  Log in to JAI.
2.  Connect to your master API Manager, see [Connect API Portal to a
    single API Manager](link_portal_to_api_manager.htm).
3.  Click **Components &gt; API Portal &gt; Additional API Managers**.
4.  Enter the public label (for example, `Environment`) for the API
    Managers. This label is shown to your API consumers in API Portal
    when listing the APIs and applications for multiple API Managers.
5.  In **Login Failure Policy**, select how failed logins are handled.
6.  Click **+** to add a slave API Manager, and enter a public name for
    it (for example, `Production`). This name is shown to your API
    consumers in API Portal when listing the APIs and applications for
    this API Manager.
7.  Enter the host IP address and port of the slave API Manager.
8.  Configure the remaining settings and click **Save**.
9.  Repeat to add additional slave API Managers.

API Portal is now connected to multiple API Managers.
