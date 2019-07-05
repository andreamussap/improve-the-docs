---
---

--- title: "Generate a CSR and import the certificate and key"
linkTitle: "Generate a CSR and import the certificate and key" date:
2019-7-3 description: &gt; You can use a Certificate Signing Request
(CSR) from a Certificate Authority (CA) to obtain certificates used by
API Gateway. Policy Studio can generate both X.509 certificates and
associated private keys. However, it cannot generate a CSR. You may need
to generate a CSR to request a CA to issue a certificate for use in the
API Gateway. --- ﻿

You can use a Certificate Signing Request (CSR) from a Certificate
Authority (CA) to obtain certificates used by API Gateway. Policy Studio
can generate both X.509 certificates and associated private keys.
However, it cannot generate a CSR. You may need to generate a CSR to
request a CA to issue a certificate for use in the API Gateway.

This topic explains how to generate a CSR using the open source OpenSSL
tool. It explains how to import the resulting certificate and
corresponding private key into Policy Studio to be used in API Gateway
configuration (for example, for SSL, signing, and encryption).

How are certificates and keys stored in API Gateway?
----------------------------------------------------

The API Gateway runtime incorporates X.509 certificate and private key
material in its configuration store. However, this is not a Java Key
Store (JKS).

A common misunderstanding is that certificates are trusted because they
are imported into API Gateway configuration, and displayed in the
**Certificates** view in Policy Studio. However, imported certificates
are not trusted by default, and must be configured in Policy Studio.

For more details, see [Manage X.509 certificates and
keys](../CommonTopics/general_certificates.htm).

What is OpenSSL?
----------------

OpenSSL is a free, popular and robust open source toolkit implementing
Secure Sockets Layer (SSL) v2 and v3, Transport Layer Security (TLS) v1,
and a full-strength general purpose cryptography library. You can use
OpenSSL to easily create CSRs. You can also use OpenSSL to create
self-signed certificates for use in SSL or message-level security
scenarios in a development environment for testing purposes. However, if
required, it is considerably faster and easier to create self-signed
developer certificates directly in Policy Studio, and there is no need
to use an external CA.

Step 1: Create a private key and CSR
------------------------------------

You can use Policy Studio to generate certificates and private keys.
However, certificates created in this way must be signed (self-signed or
by a private key already configured in the tool). In most cases, this is
not appropriate, so you should create the certificate and private key
using a 3rd party tool such as OpenSSL.

The private key is required to generate the X.509 certificate and
corresponding CSR. For example, the following command creates a private
key file named `mycompanyca.key` with a key length of 2048 bits (strong)
and a corresponding CSR in the file `mycompany.csr`:

  ----------------------------------------------------------------------------------------
  ``` {space="preserve"}
  openssl req -out mycompany.csr -new -newkey rsa:2048 -nodes \
    -keyout mycompany.key
  Country Name (2 letter code) []:US
  State or Province Name (full name) [Some-State]:Massachusetts
  Locality Name (e.g., city) []:Boston
  Organization Name (e.g., company) [My Company Ltd]:Acme
  Organizational Unit Name (e.g., section) []:
  Common Name (e.g., server's hostname) [myserver]:api.acme.comEmail 
  Address []:api-admin@acme.com
  ```
  
  ``` {space="preserve"}
  Please enter the following 'extra' attributes to be sent with your certificate request
  A challenge password []:
  An optional company name []:Acme
  ```
  ----------------------------------------------------------------------------------------

You can now submit the CSR to a public or private CA, and the CA will
issue the certificate to be imported into API Gateway for use in SSL and
message signing operations.

Step 2: Submit the CSR to the CA
--------------------------------

Each CA will have a slightly different process for submitting a CSR.
However, most CAs provide a web-based interface for this purpose. After
you submit the CSR to the appropriate CA, the CA will provide a signed
version of the certificate, which you can then import into Policy
Studio.

Step 3: Import the certificate and key into Policy Studio
---------------------------------------------------------

When you receive a signed certificate from the CA after submitting the
CSR generated earlier, you must import both the certificate and
associated private key into the appropriate key store in Policy Studio
to enable SSL and message-level security. Depending on the CA used and
how the certificate and key were generated, the certificate an key can
be in separate files or combined in a single file.

Perform the following steps:

1.  In Policy Studio, connect to an API Gateway instance.
2.  In the tree on the left, select **Certificates**. The certificates
    are displayed in the pane on the right.
3.  Depending on your use case, click the appropriate import button at
    the bottom right:
    -   Design-time: Click **Keystore**, click **Add to keystore** on
        the subsequent dialog box. This imports the certificate and
        private key into the key store for Policy Studio.
    -   Run-time: Click **Create/Import**. This import the certificate
        and private key into the runtime key store for API Gateway.
4.  Configure the **X.509 Certificate** tab as follows:
    i.  Click **Import Certificate** if the certificate is in a separate
        file. If the certificate and key are in the same file, click
        **Import Certificate + Key**.
    ii. Browse to `mycompany.crt` or the file that contains both the
        certificate and private key. Ensure that the correct file type
        is set in the file selector at the bottom right (usually
        `.pem`).
5.  Configure the **Private Key** tab as follows:
    i.  Select the **Private Key** tab (you must import both the
        certificate and the associated private key).
    ii. Click **Import Private Key**.
    iii. Browse to `mycompany.key`. Ensure that the correct file type is
        set in the file selector at the bottom right.
6.  Click **OK** to complete importing the key and certificate.

Further information
-------------------

For more details on supported security features, see the [API Management
Security Guide](/bundle/APIGateway_77_SecurityGuide_allOS_en_HTML5) .
