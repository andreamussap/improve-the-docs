---

title: " Configure scheduled report settings in Policy Studio Scheduled
report storage settings "

linkTitle: " Configure scheduled report settings in Policy Studio
Scheduled report storage settings "

date: 2019-7-5

description: > 
  API Gateway Analytics is deprecated and may be removed
  in a future release.

---

﻿

<div id="reporter_scheduled_reports_overview">

You can schedule API Gateway Analytics reports to run on a regular
basis, and to email the results to users in PDF format. These reports
include summary values at the top (for example, the number of requests,
SLA breaches, alerts triggered, and unique clients in a specified week)
followed by a table of services, and their aggregated usage data (for
example, the number of requests on each service).

The report data is for the configured *current week of the report*,
which is compared to the week before. You can set the configured
*current week of the report* to be the actual current calendar week or
any prior week (provided there is corresponding data in the database).

To configure scheduled report settings in Policy Studio, right-click the
**Environment Configuration** &gt; **Listeners** &gt; **Axway
Analytics** node in the Policy Studio tree, and select **Database
Archive**.

</div>

<div id="reporter_scheduled_reports_db_conf">

Database configuration
----------------------

Click the browse button the right, and select a pre-configured database
connection in the dialog. This setting defaults to the
`Default Database Connection`. To add a new database connection,
right-click the **Database Connections** node, and select **Add DB
connection**.

You can also edit or delete existing nodes by right-clicking and
selecting the appropriate option. Alternatively, you can add database
connections under the **Environment Configuration** &gt; **External
Connections** node in the Policy Studio tree view. For more details on
creating database connections, see the [API Gateway Policy Developer
Guide](/bundle/APIGateway_77_PolicyDevGuide_allOS_en_HTML5/) .

</div>

<div id="reporter_scheduled_reports_schedule_conf">

Scheduled reports configuration
-------------------------------

You can configure the following settings for scheduled reports:

**Enable Report Generation**:\
Select whether to enable scheduled reports in PDF format. When selected,
by default, this runs a scheduled weekly report on Monday morning at
0:01. For details on configuring a different time schedule, see the next
setting. This setting is not selected by default.

When **Enable Report Generation** is enabled, you can configure the
following settings on the **Report Generator Process** tab:

**Connect to API Gateway Analytics as User**:\
Enter the user name and password used to connect to the report generator
process. Defaults to the values entered using the `configureserver`
script.

**Output**:\
Enter the directory used for the generated report files in the **Output
Directory** field, or click **Choose** to browse to the directory.
Defaults to the directory entered using the `configureserver` script
(for example, `c:\temp\reports`). You can also select to **Do not delete
report files after emailing**. This setting is not selected by default.

</div>

<div id="reporter_scheduled_reports_smtp_conf">

SMTP configuration
------------------

When **Enable Report Generation** is enabled, you can configure the
following settings on the **SMTP** tab. These settings default to those
entered using the `configureserver` script. For more details, see the
[API Gateway Installation
Guide](/bundle/APIGateway_77_InstallationGuide_allOS_en_HTML5/) .

**Email generated reports**:\
Select whether to email generated PDF report files. This is not selected
by default.

**Do not delete report files after emailing**:\
Select whether to keep generated PDF report files after they are sent.
Not selected by default.

**Email Recipient (To)**:\
Enter the recipient of the automatically generated email (for example,
`user@mycorp.com`). Use a semicolon-separated list of email addresses to
send reports to multiple recipients.

**Email Sender (From)**:\
The generated report emails appear *from* the sender email address
specified here (for example, `no-reply@mycorp.com`).

{{&lt; alert title="Note" color="primary" &gt;}}Some mail servers do not
allow relaying mail when the sender in the **From** field is not
recognized by the server.{{&lt; /alert &gt;}}
**SMTP Server Settings**:\
Specify the following fields:

  --------------------------------- -------------------------------------------------------------------------------------------------------------
  **Outgoing Mail Server (SMTP)**   Specify the SMTP server used to relay the report email (for example, `smtp.gmail.com`).
  **Port**                          Specify the SMTP server port to connect to. Defaults to port 25.
  **Connection Security**           Select the connection security used to send the report email (`SSL`, `TLS`, or `NONE`). Defaults to `NONE`.
  --------------------------------- -------------------------------------------------------------------------------------------------------------

**Log on Using**:\
If you are required to authenticate to the SMTP server, specify the
following fields:

  --------------- -------------------------------------------------
  **User Name**   Enter the user name for authentication.
  **Password**    Enter the password for the user name specified.
  --------------- -------------------------------------------------

</div>

For more details on API Gateway Analytics, see the [API Gateway
Analytics User
Guide](/bundle/APIGateway_77_AnalyticsUserGuide_allOS_en_HTML5/) .

<div>

</div>
