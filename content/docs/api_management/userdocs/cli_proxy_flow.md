---

title: "Manage an API proxy using AMPLIFY CLI"

linkTitle: "Manage an API proxy using AMPLIFY CLI"

date: 2019-7-5

description: > 
  *Estimated reading time: 5 minutes*

---

﻿

*Estimated reading time: 5 minutes*

Before you start
----------------

-   If you are applying security to your API proxy, you will need a
    basic understanding of Basic Authentication ([RFC
    7617](https://tools.ietf.org/html/rfc7617)), or OAuth authorization
    ([RFC 6749](https://tools.ietf.org/html/rfc6749)) and JWT ([RFC
    7523](https://tools.ietf.org/html/rfc7523))
-   You will need an administrator account for AMPLIFY Central
-   Install AMPLIFY CLI:
    1.  Install Node.js 8 LTS or later
    2.  Run the following command to install AMPLIFY CLI:
    3.  ------------------------------------------
          ``` {space="preserve"}
          [sudo] npm install -g @axway/amplify-cli
          ```
          ------------------------------------------

    4.  Use `sudo` on Mac OS X or Linux if you do not own the directory
        that `npm` installs packages to. On Windows, you do not need to
        run as Administrator as `npm` installs packages into your
        AppData directory.
    5.  Run AMPLIFY package manager to install AMPLIFY Central CLI:
    6.  -----------------------------------------------
          ``` {space="preserve"}
          amplify pm install @axway/amplify-central-cli
          ```
          -----------------------------------------------

    7.  Run AMPLIFY package manager `list` command to view available
        packages:
    8.  ----------------------------------------------------------------------
          ``` {space="preserve"}
          amplify pm list
          AMPLIFY CLI, version 1.2.1
          Copyright (c) 2018, Axway, Inc. All Rights Reserved.
          NAME                           | INSTALLED VERSIONS | ACTIVE VERSION
          @axway/amplify-api-builder-cli | 1.0.1              | 1.0.1
          @axway/amplify-central-cli     | 0.1.1              | 0.1.1
          ```
          ----------------------------------------------------------------------

Objectives
----------

Learn how to authorize your DevOps service to use the AMPLIFY Central
DevOps APIs by way of AMPLIFY CLI to manage your API proxies.

-   Generate an RSA key pair for your DevOps service account
-   Create a DevOps service account in AMPLIFY Central UI
-   Authenticate your service account with AMPLIFY platform
-   Create a YAML configuration file representing your API proxy
-   Create the API proxy using AMPLIFY CLI
-   Promote the API proxy using AMPLIFY CLI
-   Test the API proxy using AMPLIFY Central UI or a REST client

Service account authentication and authorization
------------------------------------------------

To manage API proxies in AMPLIFY Central, your DevOps service account
must authenticate with AMPLIFY platform and it must be authorized to use
the AMPLIFY Central DevOps APIs.

To support DevOps service interactions, AMPLIFY Central uses the OAuth
2.0 client credentials flow with JWT:

1.  Create an RSA public private key pair for your DevOps service
    account
2.  Use the public key to register the service account with AMPLIFY
    platform to obtain a client ID
3.  Use the client ID and private key to authenticate with AMPLIFY
    platform to obtain a JWT
4.  Use the JWT to make authorized API calls to AMPLIFY Central

Generate an RSA key pair
------------------------

To authorize a DevOps service account with AMPLIFY platform, you need a
public and private key pair in RSA format. To create this key pair, use
`openssl` as follows:

  -------------------------------------------------------------------------------------
  ``` {space="preserve"}
  $ openssl genpkey -algorithm RSA -out private_key.pem -pkeyopt rsa_keygen_bits:2048
  ..............................................................+++
  .........................+++
  
  user@test123 ~/test
  $ openssl rsa -pubout -in private_key.pem -out public_key.pem
  writing RSA key
  
  user@test123 ~/test
  $ ls
  private_key.pem  public_key.pem
  ```
  -------------------------------------------------------------------------------------

Create a service account
------------------------

Log in to AMPLIFY Central UI as an administrator, and create a service
account for your DevOps service. Add the public key that you created
earlier. When the account is created, copy the client identifier from
the **Client ID** field.

Watch the animation to learn how to do this in AMPLIFY Central UI.

![Create service account in AMPLIFY Central
UI](../../Resources/Images/service_account_animation.gif){.maxWidth}

Log in to AMPLIFY CLI
---------------------

To authorize the service account with AMPLIFY platform, log in to
AMPLIFY CLI using the following command:

  --------------------------------------------------------------------------
      amplify auth login --client-id DOSA_105cf15d051c432c8cd2e1313f54c2da
  --------------------------------------------------------------------------

### Save the service account client identifier

To save the service account client identifier for future operations:

  ----------------------------------------------------------------------------------
      amplify central config set --client-id DOSA_105cf15d051c432c8cd2e1313f54c2da
  ----------------------------------------------------------------------------------

To view the saved configuration:

  --------------------------------------------------------------
      amplify central config list
      { 'client-id': 'DOSA_105cf15d051c432c8cd2e1313f54c2da' }
  --------------------------------------------------------------

Create the API configuration file
---------------------------------

The AMPLIFY Central DevOps APIs require a YAML configuration file
describing your API (the service you are proxying through AMPLIFY
Central) in terms of the API name, base path, Swagger, client
authentication, and so on. For example:

  -----------------------------------------------------------------------------------------------------------------------------
  ``` {space="preserve"}
  version: v1 # Version of the file format
  apiVersion: v1 # This version ensures backward compatibility and would not mandate a frequent update from a client side
  proxy:
      name: 'Musical Instruments secured' # name of the proxy
      basePath: /api/v1 # base path of the proxy
      swagger: 'https://ec062a054a2977120b7e721801edb38ca24dfbb3.cloudapp-enterprise.appcelerator.com/apidoc/swagger.json'
                                                                                      # optional. Swagger url of the proxy
      policy:
          clientAuth:
          type: api-key # type of client authentication policy: can be pass-through or api-key
          app: 'Sample App' # optional if policy type is pass-through
          backendAuth: # backend authentication is optional, if not specified, then no backend authentication will be enabled
          type: auth-http-basic # type of backend authentication policy: only auth-http-basic is supported now
          username: Joe # required
          password: changeme # it's allowed to be empty
      tags: ['musical', 'instruments'] # optional
      team: # the team which the proxy will be assigned to.
          name: 'Default Team'
  ```
  -----------------------------------------------------------------------------------------------------------------------------

If you specify `api-key` as the client authentication policy, you must
specify the client `app`. If the app does not already exist in AMPLIFY
Central, it is created.

`backendAuth` is an optional field. If it is not specified, no back-end
authentication is enabled. If you specify `auth-http-basic` as the
back-end authentication policy, the password can be empty.

It is best to keep the YAML configuration file in the same source
control repository as the source code of your service, so that you can
update the configuration file when you make changes to the code for your
service.

Create an API proxy
-------------------

The `create` command *creates* an API proxy if none already exists for
this API, or *updates* the existing API proxy. It returns the name of
the API proxy created.

Enter the following command to create an API proxy:

  -------------------------------------------------------------------
  ``` {space="preserve"}
  amplify central proxies create /myservices/my_service_config.yaml
  ```
  -------------------------------------------------------------------

Specify the full path to the YAML configuration file that describes your
API.

This command also supports the following options:

  ---------------------------------------------------------------------------------------------------------------------------
  Option        Description
  ------------- -------------------------------------------------------------------------------------------------------------
      --force   The default behavior is to create or update the API proxy. Use this option to force create a new API proxy.
                
      -f        
  ---------------------------------------------------------------------------------------------------------------------------

For details of the API, see [Create proxy
API](https://d-api.docs.stoplight.io/api-reference/devops-api/create-proxy).

Promote an API proxy
--------------------

The `promote` command deploys the latest revision of the API proxy to a
target runtime group. It returns the URL of the deployed API proxy and,
if you specified API key client authentication, a set of API keys to
access the API proxy.

Enter the following command to promote the latest revision of an API
proxy to the `Prod Runtime`:

  --------------------------------------------------------------------------------------------
  ``` {space="preserve"}
  amplify central proxies promote /myservices/my_service_config.yaml --target="Prod Runtime"
  ```
  --------------------------------------------------------------------------------------------

Specify the full path to the YAML configuration file that describes your
API and the target runtime group where the API proxy revision is to be
deployed.

To promote a specific revision of an API proxy that is already deployed
on a runtime group, you can optionally specify a source runtime group.
Enter the following command to promote the API proxy revision deployed
on `Test Runtime` to the `Prod Runtime`:

  --------------------------------------------------------------------------------------------------------------------
  ``` {space="preserve"}
  amplify central proxies promote /myservices/my_service_config.yaml --source="Test Runtime" --target="Prod Runtime"
  ```
  --------------------------------------------------------------------------------------------------------------------

This command supports the following options:

  Option         Description
  -------------- ------------------------------
      --source   The runtime to promote from.
      --target   The runtime to promote to.

For details of the API, see [Promote proxy
API](https://d-api.docs.stoplight.io/api-reference/devops-api/promote-proxy).

Test the API proxy
------------------

The API proxy is now accessible on the URL returned from the `promote`
command. You can test the methods and view the results in AMPLIFY
Central UI or using a REST client.

To test the API methods in AMPLIFY Central UI, select **API Proxies** in
the left navigation bar, click the appropriate API proxy in the list,
and select the **Test Methods** tab.

Review
------

You have learned how to authorize your DevOps service to use the AMPLIFY
Central DevOps APIs by way of AMPLIFY CLI to manage your API proxies.
