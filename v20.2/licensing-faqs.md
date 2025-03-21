---
title: Licensing FAQs
summary: Frequently asked questions about CockroachDB licensing.
toc: true
---

Current CockroachDB code is primarily licensed in two ways: 

-  [Business Source License (BSL)](#bsl)
-  [Cockroach Community License (CCL)](#ccl)

CockroachDB core is free to use.  Most [core features](#feature-licensing) are licensed under the BSL, but some core features are subject to the CCL or third-party licenses. 

Non-CCL core features from version 19.1 and earlier are licensed under [Apache 2.0](#apache); however, some features remain under third-party licenses. Beginning in version 19.2, these non-CCL features are licensed under the BSL for three years before [converting](#license-conversion-timeline) to the Apache 2.0 license.

CockroachDB [Enterprise features](enterprise-licensing.html) require a [paid license](#obtain-a-license) from Cockroach and are licensed under the Cockroach Community License. 

{{site.data.alerts.callout_info}}
You can find any feature's license by checking the code's file header in the [CockroachDB repository](https://github.com/cockroachdb/cockroach).
{{site.data.alerts.end}}

## Types of licenses

Type | Description
-------------|------------
<a name="apache"></a>**Apache 2.0 License** | Core features under the Apache License are free to use and fully open-source. BSL features convert to this license three years after their release. For license conversion dates, see the [table below](#license-conversion-timeline).
<a name="bsl"></a>**Business Source License**| BSL features are free to use and the source code is available, but users may not use CockroachDB [as a service](#what-constitutes-hosting-cockroachdb-as-a-service) without an agreement with Cockroach Labs. The BSL is not certified as an open-source license, but most of the [Open Source Initiative](https://en.wikipedia.org/wiki/Open_Source_Initiative) (OSI) criteria are met.
<a name="ccl"></a>**Cockroach <br/> Community License** | <ul><li> CCL (Free) features are free to use. The source code is available to view and modify, but it cannot be reused without an agreement with Cockroach Labs. </li><li> CCL (Paid) features require an Enterprise license key to access. The source code is available to view and modify, but it cannot be used without an agreement with Cockroach Labs. </li></ul>

For additional custom licensing options, [contact us](https://support.cockroachlabs.com/hc/en-us).

For each BSL release all associated alpha, beta, major, and minor (point) releases will become Apache 2.0 on the same day three years after the major release date. Once a release is published under the BSL, the license cannot be changed to prevent code from becoming open-source at the specified change date. The following table lists the current license for non-CCL features for each published release:

### License conversion timeline

CockroachDB version | License | Converts to Apache 2.0   
--------------------|---------|----------------------------
21.1 | Business Source License | May 18, 2024
20.2 | Business Source License | Nov 10, 2023 
20.1 | Business Source License | May 12, 2023  
19.2 | Business Source License | Oct 01, 2022
19.1 | Apache 2.0 | -                          
2.1 | Apache 2.0 | -
2.0 | Apache 2.0 | -

## Feature licensing

The table below shows how certain core and Enterprise features are licensed:

Feature          | BSL | CCL (free)      | CCL (paid) 
-----------------|:-----:|:-----------------:|:---------------:
**[Import](import.html)** | | ✓ |
**[Export](export.html)** | ✓ | |
**[Restore](restore.html)[*](#footnotes)** | | ✓ |
**[Full backups](take-full-and-incremental-backups.html#full-backups)[*](#footnotes)** | | ✓ |
**[Incremental backups](take-full-and-incremental-backups.html#incremental-backups)** | | | ✓
**[Other advanced backup features](backup.html)** | | | ✓
**[Core changefeed](stream-data-out-of-cockroachdb-using-changefeeds.html#create-a-core-changefeed)** | | ✓ |
**[Enterprise changefeed](stream-data-out-of-cockroachdb-using-changefeeds.html#configure-a-changefeed-enterprise)** | | | ✓
**[Table-level zone configuration](configure-replication-zones.html#replication-zone-levels)** | ✓ | |
**[Geo-partitioning](topology-geo-partitioned-replicas.html)** | | | ✓
**[Follower reads](follower-reads.html)** | | | ✓
**[Node map](enable-node-map.html)** | | | ✓
**[Locality-aware index selection](cost-based-optimizer.html#preferring-the-nearest-index)** | | | ✓
**[Encryption at rest](encryption.html#encryption-at-rest-enterprise)** | | | ✓
**[Role-based access management](authorization.html#roles)** | ✓ | |
**[Password and certificate authentication](authentication.html)** | ✓ | |
**[GSSAPI with Kerberos authentication](gssapi_authentication.html)** | | | ✓
**[All other core features](https://www.cockroachlabs.com/compare)** | ✓ | |

<a name="footnotes"></a><sup>* Now a core feature in v20.2.</sup>

{{site.data.alerts.callout_info}}
Individual feature licensing may change with each release of CockroachDB. You can use the dropdown menu at the top of the page to view documentation for other versions of CockroachDB.
{{site.data.alerts.end}}

{{site.data.alerts.callout_info}}
More information about all Enterprise features can be found [here](enterprise-licensing.html).
{{site.data.alerts.end}}

## Obtain a license

All CockroachDB code is included in the same binary. No license key is required to access BSL and CCL (Free) features. To access CCL (Paid) features, users have two options:

- An **Enterprise License** enables you to use CockroachDB Enterprise features for longer periods (one year or more). To upgrade to an Enterprise license, <a href="mailto:sales@cockroachlabs.com">contact Sales</a>.
- A **Trial License** enables you to try out CockroachDB Enterprise features for 30 days for free. To obtain a trial license, fill out [the registration form](https://www.cockroachlabs.com/get-cockroachdb/) and receive your trial license via email within a few minutes.

{{site.data.alerts.callout_success}}
For quick local testing of Enterprise features, you can use the [`cockroach demo`](cockroach-demo.html) command, which starts a temporary, in-memory cluster with a SQL shell open and a trial license applied automatically.
{{site.data.alerts.end}}

{{site.data.alerts.callout_info}}
Cockroach Labs is willing to offer self-hosted CockroachDB Enterprise features free of charge and discounted prices for {{ site.data.products.db }} to select non-profit organizations and non-commercial academic projects. To learn more, please [contact us](https://support.cockroachlabs.com/hc/en-us).
{{site.data.alerts.end}}

## Set a license

As the CockroachDB `root` user, open the [built-in SQL shell](cockroach-sql.html) in insecure or secure mode, as per your CockroachDB setup. In the following example, we assume that CockroachDB is running in insecure mode. Then use the [`SET CLUSTER SETTING`](set-cluster-setting.html) command to set the name of your organization and the license key:

{% include copy-clipboard.html %}
~~~ shell
$ cockroach sql --insecure
~~~

{% include copy-clipboard.html %}
~~~ sql
>  SET CLUSTER SETTING cluster.organization = 'Acme Company';
~~~

{% include copy-clipboard.html %}
~~~ sql
>  SET CLUSTER SETTING enterprise.license = 'xxxxxxxxxxxx';
~~~

## Verify a license

To verify a license, open the [built-in SQL shell](cockroach-sql.html) and use the [`SHOW CLUSTER SETTING`](show-cluster-setting.html) command to check the organization name and license key:

{% include copy-clipboard.html %}
~~~ sql
>  SHOW CLUSTER SETTING cluster.organization;
~~~
~~~
  cluster.organization
+----------------------+
  Acme Company
(1 row)
~~~

{% include copy-clipboard.html %}
~~~ sql
>  SHOW CLUSTER SETTING enterprise.license;
~~~
~~~
             enterprise.license
+-------------------------------------------+
  crl-0-ChB1x...
(1 row)
~~~

The license setting is also logged in the cockroach.log on the node where the command is run:

{% include copy-clipboard.html %}
~~~ sql
$ cat cockroach.log | grep license
~~~
~~~
I171116 18:11:48.279604 1514 sql/event_log.go:102  [client=[::1]:56357,user=root,n1] Event: "set_cluster_setting", target: 0, info: {SettingName:enterprise.license Value:xxxxxxxxxxxx User:root}
~~~

## Monitoring for license expiry

<span class="version-tag">New in v20.2</span> You can monitor the time until your license expires with [Prometheus](monitor-cockroachdb-with-prometheus.html). The `seconds_until_enterprise_license_expiry` metric reports the number of seconds until the Enterprise license on a cluster expires. It will report 0 if there is no license or a negative number if the license has already expired. For more information, see [Monitoring and Alerting](monitoring-and-alerting.html).

## Renew an expired license

After your license expires, the Enterprise features stop working, but your production setup is unaffected. For example, the backup and restore features would not work until the license is renewed, but you would be able to continue using all other features of CockroachDB without interruption.

To renew an expired license, <a href="mailto:sales@cockroachlabs.com">contact Sales</a> and then [set](licensing-faqs.html#set-a-license) the new license.

## FAQs

### Can I host CockroachDB as a service for internal use at my organization?

Yes, employees and contractors can use your internal CockroachDB instance as a service, but no people outside of your organization will be able to use it without purchasing a license. Use of Enterprise features will always require a license.

### What constitutes hosting CockroachDB as a service?

Hosting CockroachDB as a service means creating an offering that allows third parties (other than your employees and contractors) to operate a database. Specifically, third parties cannot modify table schemas.

### I would like to reuse a single component from the CockroachDB project in my own software, which uses the AGPL or another open-source license. Is this possible?

The CockroachDB team is committed to supporting the open-source community and willing to consider extracting specific internal components that are generally useful as a separate project with its own license, for example APL. For more details, feel free to [contact us](https://support.cockroachlabs.com/hc/en-us).

### Can I fork the CockroachDB project pre-BSL and create my own CockroachDB derivative with a different license?

You can fork any historical version of CockroachDB in your own project, as allowed by the license available for that version, and modify it for your purpose. Note however that only the copyright holder (Cockroach Labs) can relicense the components that you forked from: your derivative will need to keep the original license at the time of the fork. Any component you copy from a BSL-licensed CockroachDB into your project will make the BSL apply to your project as well.