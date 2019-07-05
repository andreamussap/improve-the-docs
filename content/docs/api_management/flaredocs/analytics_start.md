---

title: "Launch API Gateway Analytics"

linkTitle: "Launch API Gateway Analytics"

date: 2019-7-5

description: > 
  This topic explains how to launch the API Gateway
  Analytics web console used to monitor your API Gateway domain. It also
  explains how to change the default API Gateway Analytics user
  credentials and sample certificate for TLS.

---

﻿

This topic explains how to launch the API Gateway Analytics web console
used to monitor your API Gateway domain. It also explains how to change
the default API Gateway Analytics user credentials and sample
certificate for TLS.

This topic assumes that you have already performed the steps in
[Configure API Gateway with the metrics
database](../CommonTopics/metrics_gw_config.htm).

Start API Gateway Analytics
---------------------------

To launch API Gateway Analytics, perform the following steps:

1.  Start the API Gateway Analytics server using the `analytics` script
    in the `/bin` directory of your API Gateway Analytics installation.
2.  Using the default port, connect to the API Gateway Analytics
    interface in a browser at the following URL:
3.  `HOST` points to the IP address or hostname of the machine on which
    API Gateway Analytics is installed.
4.  Log in using the username and password that you selected when
    installing API Gateway Analytics. See also [Change the default API
    Gateway Analytics credentials](#Change).

### []{#Start}Start API Gateway Analytics as a service

You can also run the API Gateway Analytics server as a service by
creating a script. A sample script and *ReadMe* is provided in the
following directory:

  ---------------------------------------------------
  `INSTALL_DIR/analytics/posix/samples/etc/init.d/`
  ---------------------------------------------------

{{&lt; alert title="Note" color="primary" &gt;}}If you change to another
metrics database that has a different set of remote hosts or clients
configured, you must restart both API Gateway and API Gateway
Analytics.{{&lt; /alert &gt;}}

[]{#Change}Change the default API Gateway Analytics credentials
---------------------------------------------------------------

You can change the default API Gateway Analytics user credentials by
editing your API Gateway Analytics configuration in Policy Studio under
**Environment Configuration** &gt; **Users and Groups**.

The default `admin` user is local to API Gateway Analytics. Updating
these credentials does not affect access to other Axway products.

Replace the default sample certificate for API Gateway Analytics
----------------------------------------------------------------

The default `Samples Test Certificate` certificate used for SSL/TLS is
self-signed by Axway. You can replace this default sample certificate by
editing the API Gateway Analytics configuration in Policy Studio:

1.  Create a new project from an existing configuration, and select the
    configuration in `INSTALL_DIR/analytics/conf/fed`.
2.  Select **Environment Configuration** &gt; **Certificates and Keys**,
    and add your own user-signed certificate.
3.  Select **Environment Configuration** &gt; **Listeners** &gt; **Axway
    Analytics** &gt; **Management Services** &gt; **Ports**, and edit
    the **Reporter HTTP Interface**.
4.  On the **Network** tab, click **X.509 Certificate** to change the
    SSL certificate.
5.  Save the project, and copy your Policy Studio project files back to
    your API Gateway Analytics installation. For example, copy the
    `.xml` files in `users/apiprojects/my-project` to
    `INSTALL_DIR/analytics/conf/fed`.

<div>

Further information
-------------------

For more details, see [Monitor traffic in API Gateway
Analytics.](analytics_monitoring.htm)

</div>
