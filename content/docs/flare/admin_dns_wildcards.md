---
---

--- title: "Configure a DNS service with wildcards for virtual hosting"
linkTitle: "Configure a DNS service with wildcards for virtual hosting"
date: 2019-7-3 description: &gt; The Domain Name System (DNS) is a
hierarchical distributed naming system for resources on the Internet or
a private network. It translates domain names to numerical IP addresses
used to locate services and devices worldwide, and associates details
with domain names assigned to each resource. You can use wildcard DNS
records to specify multiple domain names by using an asterisk in the
domain name (such as `*.example.com`). --- ﻿

The Domain Name System (DNS) is a hierarchical distributed naming system
for resources on the Internet or a private network. It translates domain
names to numerical IP addresses used to locate services and devices
worldwide, and associates details with domain names assigned to each
resource. You can use wildcard DNS records to specify multiple domain
names by using an asterisk in the domain name (such as `*.example.com`).

A virtual host is a server, or pool of servers, that can host multiple
domain names (for example, `company1.api.example.com` and
`company2.api.example.com`). To use virtual hosting for the you need to
be able to use different host names to refer to the same destination
server. A convenient way to do this is by using a DNS service to specify
wildcard entries for physical host names (for example, by setting
`*.example.com` to `192.0.2.11`).

This setting enables you to run more than one website, or set of REST
APIs, on a single host machine (in this case, `192.0.2.11` ). Each
domain name can have its own host name, paths, APIs, and so on. For
example:

  ---------------------------------------------------
  ``` {space="preserve"}
  https://company1.api.example.com:8080/api/v1/test
  https://company2.api.example.com:8080/api/v2/test
  ```
  ---------------------------------------------------

{{&lt; alert title="Note" color="primary" &gt;}}This topic explains how
to set up DNS wildcards for virtual hosts. This is a prerequisite for
configuring the for virtual hosts. For more details, see the [API
Gateway Policy Developer
Guide](/bundle/APIGateway_77_PolicyDevGuide_allOS_en_HTML5/) .{{&lt;
/alert &gt;}}

DNS workflow
------------

When a client makes an HTTP request to a specific URL, such as
`http://www.example.com`, the client must decide which IP address to
connect to. In this case, the client makes a request on the local name
service for the address of the `www.example.com` machine. This usually
involves the following workflow:

1.  Check the `/etc/hosts` file for the specified name, and if that
    fails, make a DNS request.
2.  Send the DNS request to the default DNS server for the client
    system, which refers the client to the server for `example.com`
    (referral), or makes the request on the client's behalf (recursion).
3.  The DNS server that manages the `www.example.com` domain responds
    with a record that specifies the IP address of `www.example.com`.
4.  The client contacts that IP address, and includes a `Host` header
    when making its request (`Host:www.example.com`).
5.  The server can use this `Host` header to distinguish requests to
    different sites (virtual hosts) that use the same physical hardware
    and HTTP server process.

You can divide a parent domain such as `example.com` into subdomains,
one for each hosted site. This provides each site with its own distinct
namespace (for example, `company1.example.com`, `company2.example.com`,
`company3.example.com`, and so on).

When using API Manager, you can divide the parent domain (for example,
`apiportal.io`) into subdomains, one for each hosted organization. This
provides each organization with its own distinct namespace (for example,
`company1.apiportal.io`, `company2.apiportal.io`,
`company3.apiportal.com`, and so on).

BIND DNS software
-----------------

Internet Systems Consortium (ISC) BIND is the de facto standard DNS
server used on the Internet. It works on a wide variety of Linux
systems, and on Microsoft Windows. Windows Server operating systems can
also use the Windows DNS service. The examples in this topic describe
the configuration for BIND only. For more details, see
<http://www.isc.org/downloads/bind/>.

Linux systems generally enable the configuration of BIND to be installed
using a package manager. For example, for Ubuntu systems, you can use
the following command:

  ----------------------------------
      $ sudo apt-get install bind9
  ----------------------------------

The service and packages are generally called `bind`, `bind9`, or
`named`. For example, on Ubuntu, you can restart BIND using the
following command:

  ----------------------------------
  ``` {space="preserve"}
  $ sudo /etc/init.d/bind9 start 
  $ sudo /etc/init.d/bind9 stop 
  $ sudo /etc/init.d/bind9 restart
  ```
  ----------------------------------

Configure a wildcard domain
---------------------------

You can configure BIND using the `named.conf` configuration file, which
is typically installed in one of the following locations:

  ------------------------------------------
      /etc/named.conf /etc/bind/named.conf
  ------------------------------------------

This configuration file is typically set up with `include` entries to
make configuration and upgrade easier, and which should be easy to
follow. The simple example described in this topic uses a flat file
only. There are two main parts to the configuration:

-   `options` control the behavior of the service
-   `zone` indicates how each autonomous part of the domain name tree
    should behave

The example shown in this topic uses the simplest options. This example
also shows a single domain used for API Manager./p&gt;

### Configure DNS options

The following are some example DNS options that you can configure for
your installation in the `named.conf` file:

  -----------------------------------------------------------------
  ``` {space="preserve"}
  options { 
     directory "."; // e.g., /var/named 
     listen-on port 8866 { any; }; // remove this for production 
     pid-file "named.pid"; 
     allow-recursion { any; }; 
     allow-transfer { any; }; 
     allow-update { any; }; 
     forwarders { 10.253.253.253; 10.252.252.252; }; 
  };
  ```
  -----------------------------------------------------------------

The example options are described as follows:

Option

Description

`directory`

This is normally set to a directory on a writeable filesystem, such as
`/var/bind`. This example is set to the current directory (`"."`).

`listen-on port`

This example is set to `8866` for testing, but you should leave this
blank for real DNS use in a production environment. Most DNS clients
such as Web browsers always request on the standard DNS port `53`. You
can also configure debugging tools such as `dig` and `nslookup` to try
other ports.

`pid-file`

This example sets up `named` to run in the current directory (or
`/var/named` if configured), and gives it a name to store its lock file.

`allow-`

The example `allow-` options are permissive, and allow any external host
to use this name server, update it dynamically, and transfer a dump of
its domains. This makes it unsuitable for external exposure. To restrict
these `allow-` options to the local system, change the `any` settings to
`127.0.0.1`.

`forwarders`

Requests that cannot be serviced locally are forwarded to the specified
servers, which are the default DNS servers for the site. Specifying
`forwarders` enables you to use this name server as your local DNS. It
can forward requests for sites outside your domain (for example,
`example.com` or `apiportal.io` to the forwarding name servers. This
enables your test environment to coexist with your normal name servers.

### Configure default zones

The next step is to configure default zones for the `127.0.0.1` address.
For example:

  -----------------------------------
  ``` {space="preserve"}
  zone "localhost" IN { 
     type master; 
     file "localhost.zone"; 
     allow-transfer { any; }; 
  }; 
  ```
  
  ``` {space="preserve"}
  zone "0.0.127.in-addr.arpa" IN { 
     type master; 
     file "127.0.0.zone"; 
     allow-transfer { any; };
  };
  ```
  -----------------------------------

These settings configure addresses for mapping `localhost` to
`127.0.0.1`, and mapping `127.0.0.1` back to `localhost` when required.
These example options are described as follows:

Option

Description

`type`

Specifies that this is the `master` server for `localhost`.

`file`

Specifies the domain zone file that contains the records for the domain
(for more details,see [Configure domain zone files](#Configur)).

`allow-transfer`

Specifies addresses that are allowed to transfer zone information from
the zone server. The default `any` setting means that the contents of
the domain can be transferred to anyone.

### Configure logging

The following settings provide some default logging configuration:

  ------------------------------
  ``` {space="preserve"}
  logging { 
     channel default_syslog { 
       file "named.log";  
       severity debug 10; 
     };
  }; 
  ```
  ------------------------------

For example, you can change the `file` setting to `/var/log/named.log`,
or change the severity level.

### Configure a wildcard domain

You must now configure your wildcard domain. Here you specify the name
of the domain for which to serve wildcard addresses. For example:

  ---------------------------
  ``` {space="preserve"}
  zone "example.com." IN { 
     type master; 
     file "example.zone"; 
     allow-transfer { any; 
     }; 
  };
  ```
  ---------------------------

This is almost identical to the `localhost` zone already configured.

Similarly, for an API Manager domain:

  ----------------------------
  ``` {space="preserve"}
  zone "apiportal.io." IN { 
     type master; 
     file "apiportal.zone"; 
     allow-transfer { any; 
     }; 
  };
  ```
  ----------------------------

### []{#Configur}Configure domain zone files

Finally, your `zone` configuration now includes references to two
separate domain zone files (in this case, `localhost.zone` and your
wildcard zone (`example.zone` or `apiportal.zone`. These domain zone
files use a standard format, which is defined in RFC
1035:<http://www.ietf.org/rfc/rfc1035.txt>. For more details, see also
Wikipedia:<http://en.wikipedia.org/wiki/Zone_file>.

The domain zone files dictate how the server responds to requests for
data in the specified zone. For example, your basic `localhost.zone`
domain is as follows:

  ------------------------------------------------------------------------
  ``` {space="preserve"}
  @  IN SOA  root.acmecorp.com. ( 
             20131021  ; serial number of zone file (yyyymmdd##) 
             3H        ; refresh time
             15M       ; retry time in case of problem 
             1W        ; expiry time
             1D )      ; maximum caching time in case of failed lookups 
     IN NS   @ 
     IN A    127.0.0.1
  ```
  ------------------------------------------------------------------------

In this file, `@` is shorthand for the domain, and describes the first
(and only) record in the file. `@` specifies the name of the zone, and
has the following associated records:

-   `SOA` specifies details about the zone, including various serial and
    timer settings
-   `NS` specifies that the name server for this domain is `localhost`
-   `A` specifies that the address for localhost is `127.0.0.1`

Your wildcard domain is similar. For example, the contents of
`example.zone` or `apiportal.zone`are as follows:

  -------------------------------------------------------------------------
  ``` {space="preserve"}
  @  IN SOA  . root.acmecorp.com. ( 
               20130903  ; serial number of zone file (yyyymmdd##)
               604800    ; refresh time 
               86400     ; retry time in case of problem  
               2419200   ; expiry time
               604800)   ; maximum caching time in case of failed lookups
     IN NS     .
     IN A      192.168.0.10 
  *  IN A      192.168.0.10
  ```
  -------------------------------------------------------------------------

This domain has the `SOA`, `NS`, and `A` records like the `localhost`
zone, but also adds a `*` record. This matches any subdomain of
`example.com` or `apiportal.io` to resolve to the specified IP address.
