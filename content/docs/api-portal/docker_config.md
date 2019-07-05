--- title: "Customize API Portal topology in Docker" linkTitle:
"Customize API Portal topology in Docker" date: 2019-7-3 description:
&gt; This section describes how to create Docker data volumes for
persistence, edit your `docker-compose.yml` file to customize your
Docker-based API Portal topology to suit your environment, and deploy
API Portal using `docker-compose up`. --- ﻿

This section describes how to create Docker data volumes for
persistence, edit your `docker-compose.yml` file to customize your
Docker-based API Portal topology to suit your environment, and deploy
API Portal using `docker-compose up`.

[]{#Create}Create Docker data volumes for persistence
-----------------------------------------------------

You must create Docker data volumes to persist API Portal customization
data. Using data volumes can also prevent data loss when the container
reboots or crashes, or when you are upgrading or setting up HA for an
API Portal Docker deployment.

The data from the database container is automatically stored in a Docker
volume when the database container is initialized. The data volume
created for the database container by default is "unnamed", which means
that the Docker engine assigns a random name for this data volume. To
use this volume (for example, to add an additional database container or
to upgrade the database container itself) you can inspect the current
database container and identify the name and the destination of the data
volume.

The data volumes are stored on the Docker host machine and as such
consume disk space. You should delete unused data volumes regularly.

The following table describes which API Portal assets should be stored
in a Docker volume to preserve the customizations during upgrade or HA
setup of an API Portal Docker deployment.

Data type

Storage location

Example data volume name

**System**

The certificate from the API Manager

`INSTALL_DIR/administrator/components/com_apiportal/assets/cert`

apiportal-cert

The certificates used by API Portal

`/etc/httpd/conf`

apiportal-conf

API Portal's vhost file and other config files

`/etc/httpd/conf.d`

apiportal-confd

Public API mode encryption key file

`/apiportal/encryption`

apiportal-encryption

**Customization**

Joomla! templates

`INSTALL_DIR/templates`

apiportal-templates

**Localization**

Installed languages in front end UI

`INSTALL_DIR/language`

apiportal-language

Installed languages on Joomla! Administrator Interface (JAI)

`INSTALL_DIR/administrator/language`

apiportal-adm-language

**Attachments uploaded to Joomla! content**

Images

`INSTALL_DIR/images`

apiportal-images

Media

`INSTALL_DIR/media`

apiportal-media

Update docker-compose.yml for your environment
----------------------------------------------

The Docker sample package includes a sample `docker-compose.yml` file.
This file describes:

-   The services and containers to run
-   The properties to run those services and containers with
-   The correct sequence to start those services

The `docker-compose.yml` file enables you to run API Portal using a
single `docker-compose up` command.

To run API Portal you need to have at least two services:

-   Database
-   API Portal

**Database service example**:

  -----------------------------------------
  ``` {space="preserve"}
    db:
         image: mysql:5.6
          networks:
          - apiportal
          env_file:
          - .env
          volumes:
          - "apiportal-db:/var/lib/mysql"
  ```
  -----------------------------------------

**API Portal service example**:

  ------------------------------------------------------------------------------------------------------
  ``` {space="preserve"}
    webapp:
      build: .
      depends_on:
      - db
      ports:
      - "443:443"
      networks:
      - apiportal
      env_file:
      - .env
      volumes:
      - "apiportal-cert:/opt/axway/apiportal/htdoc/administrator/components/com_apiportal/assets/cert"
      - "apiportal-templates:/opt/axway/apiportal/htdoc/templates"
      - "apiportal-language:/opt/axway/apiportal/htdoc/language"
      - "apiportal-adm-language:/opt/axway/apiportal/htdoc/administrator/language"
      - "apiportal-conf:/etc/httpd/conf"
      - "apiportal-confd:/etc/httpd/conf.d"
      - "apiportal-encryption:/apiportal/encryption"
  ```
  ------------------------------------------------------------------------------------------------------

Run API Portal using docker-compose up
--------------------------------------

After you have changed the `docker-compose.yml` file, perform the
following to run API Portal:

1.  Ensure that you are logged in to your Docker host machine as the
    `root` user.
2.  Change to the directory where you unzipped the Docker sample
    package.
3.  Copy the file `.env.demo` to `.env`.
4.  Update the `.env` file with the appropriate settings for your API
    Portal.
5.  Enter the following command to start API Portal in attached mode:
6.  -------------------------
          $ docker-compose up
      -------------------------

Wait for the following message to be displayed:

  --------------------------------------------------------------------------------------------------------------
  ``` {space="preserve"}
  "AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using <IP address>. 
  Set the 'ServerName' directive globally to suppress this message".
  ```
  --------------------------------------------------------------------------------------------------------------

This message indicates that Apache has been started and you can start
using API Portal.

API Portal is now running in a Docker container.

Before you can use API Portal, you must link it to your API Manager. For
more details, see [Connect API Portal to API
Manager](../../../APIPortalInstallGuideTopics/connect_to_apimgr.htm).

### Environment variable settings

  -------------------------------------------------------------------------------------------------------------------------
  ``` {space="preserve"}
  # required variables
  MYSQL_HOST=db
  MYSQL_PORT=3306
  MYSQL_ROOT_PASSWORD=abc123
  MYSQL_USERNAME=joomla
  MYSQL_PASSWORD=changeme
  MYSQL_DBNAME=joomla
  APIMANAGER_PUBLIC_NAME=Master
  APIMANAGER_HOST=127.0.0.1
  APIMANAGER_PORT=8075
  
  # optional variables
  APIPORTAL_SSO_ON=1 – enable sso, default is 0
  APIPORTAL_SSO_PATH=sso – entrypoint for sso (default is sso)
  APIPORTAL_SSO_ENTITY_ID=asdf -sso client entity id (default is empty) 
  APIPORTAL_SSO_WHITELIST=keycloak.int.acme.com – whitelist containing allowed sso hostname or ip (default is empty)
  APIPORTAL_LOGIN_PROTECTION_ON=1 – enable login protection (default is 0)
  APIPORTAL_LOGIN_PROTECTION_ATTEMPTS_BEFORE_RECAPCHA=3 – login attempts before recapcha (default is 3)
  APIPORTAL_LOGIN_PROTECTION_ATTEMPTS_BEFORE_LOCK=3  – login attempts before account is temporarily locked (default is 3)
  APIPORTAL_LOGIN_PROTECTION_LOCK_DURATION_SEC=300 – account lock time in seconds (default is 300)
  APIPORTAL_LOGIN_PROTECTION_LOCK_BY_IP=1 – enable login protection by IP (default is 0)
  # OAUTH_WHITELIST_ITEM_*  list of trusted OAuth hosts. For example: 
  OAUTH_WHITELIST_ITEM_1=https://apiportal1.axway.com
  OAUTH_WHITELIST_ITEM_2=https://apiportal2.axway.com
  REDIS_CACHE_TIMEOUT_SEC=300 – redis cache timeout in seconds  (default is empty)
  ```
  -------------------------------------------------------------------------------------------------------------------------
