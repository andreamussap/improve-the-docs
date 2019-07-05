---

title: "AMPLIFY Central DevOps overview"

linkTitle: "AMPLIFY Central DevOps overview"

date: 2019-7-5

description: > 
        *Estimated reading time: 3 minutes*

---

﻿

*Estimated reading time: 3 minutes*

Understand why and how to integrate AMPLIFY Central in your DevOps
pipeline.

What is DevOps?
---------------

DevOps can be defined as a combination of people, processes, and
technology that increases your organization's ability to quickly deliver
quality services to your customers.

-   People: Developers, operations, product owners, testers,
    infrastructure engineers, and so on, all work together
    collaboratively as a team to build and deliver the service.
-   Process: Continuous improvement of the process to deliver a quality
    service is key.
-   Technology: Tools that automate the manual processes and integrate
    with other tools to make it easier and faster to develop, package,
    test, release, and deploy the service.

AMPLIFY Central DevOps approach
-------------------------------

Taking a DevOps approach to API management with AMPLIFY Central enables
you to streamline the delivery of updates or new capabilities in your
services to your customers and partners.

![AMPLIFY Central in your DevOps
pipeline](../../Resources/Images/devops.png){.maxWidth}

AMPLIFY Central is a CI/CD native solution that you can easily snap in
to your existing DevOps pipeline. Simplify and customize how you manage
APIs across your organization by using the AMPLIFY CLI and the AMPLIFY
Central DevOps API to manage your API proxies in AMPLIFY Central.

AMPLIFY Central DevOps API
--------------------------

The AMPLIFY Central DevOps API is a DevOps-friendly REST API that you
can use to create, update, deploy, and promote API proxies to AMPLIFY
Central runtimes. It provides the following DevOps-friendly features:

-   The APIs accept a YAML configuration file which represents the
    desired state of the API proxy.
    -   YAML format is human-readable, easily diffed, and maintainable.
    -   YAML configuration file can be versioned along with the code for
        the service in a version control system, such as Git.
-   The APIs are coarse-grained, avoiding the need for complex ordered
    API invocations to multiple fine-grained APIs.
-   The APIs use an update or insert (*upsert*) strategy. If the API
    proxy already exists it is updated, otherwise it is created.

Visit our [API documentation](https://d-api.docs.stoplight.io/).

### DevOps authentication and authorization

To use the AMPLIFY Central DevOps API in your DevOps pipeline, your
DevOps service (for example, Jenkins) must be authenticated with AMPLIFY
platform and it must be authorized to use the DevOps API. Use AMPLIFY
CLI to log in to AMPLIFY platform with a service account and obtain an
access token to perform authorized API calls. For detailed steps, see
[Manage an API proxy using AMPLIFY CLI](cli_proxy_flow.htm).

### Example DevOps flow

The following shows an example DevOps flow to create, deploy, and
promote an API proxy using Git and Jenkins:

1.  A developer makes a change to the code for the service, updates the
    YAML configuration file for the desired state of proxy, and commits
    the changes to Git.
2.  Jenkins build triggers and checks out the latest code and YAML from
    Git.
3.  Jenkins calls AMPLIFY CLI command `amplify auth login` to
    authenticate to AMPLIFY platform and obtain an access token.
4.  Jenkins calls AMPLIFY CLI command `amplify apic create` to create or
    update the API proxy for this service.
    -   CLI calls the DevOps API `POST /proxies` to create the API proxy
        in AMPLIFY Central. See [Create proxy API
        reference](https://d-api.docs.stoplight.io/new-subpage/devops-api/create-proxy).
5.  Jenkins calls AMPLIFY CLI command `amplify apic promote` to deploy
    the API proxy to the test runtime.
    -   CLI calls the DevOps API `POST /promote` to deploy the API proxy
        on the test runtime. See [Promote proxy API
        reference](https://d-api.docs.stoplight.io/new-subpage/devops-api/promote-proxy).
6.  Automated tests are run on the AMPLIFY Central test runtime.
7.  Jenkins calls AMPLIFY CLI command `amplify apic promote` to promote
    the API proxy from the test runtime to the production runtime.
    -   CLI calls the DevOps API `POST /promote` to deploy the API proxy
        on the production runtime.
