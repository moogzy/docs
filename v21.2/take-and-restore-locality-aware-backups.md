---
title: Take and Restore Locality-aware Backups
summary: Learn about the advanced options you can use when you backup and restore a CockroachDB cluster.
toc: true
---

The ability to [backup a full cluster](backup.html#backup-a-cluster) has been added and the syntax for [incremental backups](backup.html#create-incremental-backups) is simplified. Because of these two changes, [basic backup usage](take-full-and-incremental-backups.html) is now sufficient for most CockroachDB clusters. However, you may want to control your backup and restore options more explicitly.

This doc provides information about how to take and restore locality-aware backups.

{{site.data.alerts.callout_info}}
Locality-aware [`BACKUP`](backup.html) is an [Enterprise-only](https://www.cockroachlabs.com/product/cockroachdb/) feature. However, you can take [full backups](take-full-and-incremental-backups.html) without an Enterprise license.
{{site.data.alerts.end}}

You can create locality-aware backups such that each node writes files only to the backup destination that matches the [node locality](configure-replication-zones.html#descriptive-attributes-assigned-to-nodes) configured at [node startup](cockroach-start.html).

This is useful for:

- Reducing cloud storage data transfer costs by keeping data within cloud regions.
- Helping you comply with data domiciling requirements.

A locality-aware backup is specified by a list of URIs, each of which has a `COCKROACH_LOCALITY` URL parameter whose single value is either `default` or a single locality key-value pair such as `region=us-east`. At least one `COCKROACH_LOCALITY` must be the `default`. Given a list of URIs that together contain the locations of all of the files for a single locality-aware backup, [`RESTORE` can read in that backup](#restore-from-a-locality-aware-backup).

{{site.data.alerts.callout_info}}
The locality query string parameters must be [URL-encoded](https://en.wikipedia.org/wiki/Percent-encoding).
{{site.data.alerts.end}}

During locality-aware backups, backup file placement is determined by leaseholder placement, as each node is responsible for backing up the ranges for which it is the leaseholder.  Nodes write files to the backup storage location whose locality matches their own node localities, with a preference for more specific values in the locality hierarchy.  If there is no match, the `default` locality is used.

## Create a locality-aware backup

For example, to create a locality-aware backup where nodes with the locality `region=us-west` write backup files to `s3://us-west-bucket`, and all other nodes write to `s3://us-east-bucket` by default, run:

{% include copy-clipboard.html %}
~~~ sql
> BACKUP INTO
	  ('s3://us-east-bucket?COCKROACH_LOCALITY=default', 's3://us-west-bucket?COCKROACH_LOCALITY=region%3Dus-west');
~~~

can be restored by running:

{% include copy-clipboard.html %}
~~~ sql
> RESTORE FROM ('s3://us-east-bucket', 's3://us-west-bucket');
~~~

Note that the first URI in the list has to be the URI specified as the `default` URI when the backup was created. If you have moved your backups to a different location since the backup was originally taken, the first URI must be the new location of the files originally written to the `default` location.

{{site.data.alerts.callout_info}}
For guidance on connecting to other storage options or using other authentication parameters, read [Use Cloud Storage for Bulk Operations](use-cloud-storage-for-bulk-operations.html).
{{site.data.alerts.end}}

## Restore from a locality-aware backup

Given a list of URIs that together contain the locations of all of the files for a single [locality-aware backup](#create-a-locality-aware-backup), [`RESTORE`](restore.html) can read in that backup. Note that the list of URIs passed to [`RESTORE`](restore.html) may be different from the URIs originally passed to [`BACKUP`](backup.html). This is because it's possible to move the contents of one of the parts of a locality-aware backup (i.e., the files written to that destination) to a different location, or even to consolidate all the files for a locality-aware backup into a single location.

When restoring a [full backup](take-full-and-incremental-backups.html#full-backups), the cluster data is restored first, then the system table data "as is." This means that the restored zone configurations can point to regions that do not have active nodes in the new cluster. For example, if your full backup has the following [zone configurations](configure-zone.html):

~~~ sql
> ALTER PARTITION europe_west OF INDEX movr.public.rides@primary \
		CONFIGURE ZONE USING constraints = '[+region=europe-west1]';

> ALTER PARTITION us_east OF INDEX movr.public.rides@primary \
		CONFIGURE ZONE USING constraints = '[+region=us-east1]';

> ALTER PARTITION us_west OF INDEX movr.public.rides@primary \
		CONFIGURE ZONE USING constraints = '[+region=us-west1]';
~~~

And the restored cluster does not have [nodes with the locality](partitioning.html#node-attributes) `region=us-west1`, the restored cluster will still have a zone configuration for `us-west1`. This means that the cluster's data will _not_ be reshuffled to `us-west1` because the region does not exist. The data will be distributed as if the zone configuration does not exist. For the data to be distributed correctly, you can [add node(s)](cockroach-start.html) with the missing region or [remove the zone configuration](configure-zone.html#remove-a-replication-zone).

For example, a locality-aware backup created with:

{% include copy-clipboard.html %}
~~~ sql
> BACKUP INTO
	  ('s3://us-east-bucket?COCKROACH_LOCALITY=default', 's3://us-west-bucket?COCKROACH_LOCALITY=region%3Dus-west')
~~~

The backup above can be restored by running:

{% include copy-clipboard.html %}
~~~ sql
> RESTORE FROM
  	('s3://us-east-bucket/{path/to/backup/subdirectory}', 's3://us-west-bucket/{path/to/backup/subdirectory}');
~~~

To view the available subdirectories, use [`SHOW BACKUPS`](restore.html#view-the-backup-subdirectories).

{{site.data.alerts.callout_info}}
[`RESTORE`](restore.html) is not truly locality-aware; while restoring from backups, a node may read from a store that does not match its locality. This can happen in the cases that either the [`BACKUP`](backup.html) or [`RESTORE`](restore.html) was not of a [full cluster](take-full-and-incremental-backups.html#full-backups). Note that during a locality-aware restore, some data may be temporarily located on another node before it is eventually relocated to the appropriate node. To avoid this, you can [manually restore zone configurations from a locality-aware backup](#manually-restore-zone-configurations-from-a-locality-aware-backup).
{{site.data.alerts.end}}

## Create an incremental locality-aware backup

If you backup to a destination already containing a [full backup](take-full-and-incremental-backups.html#full-backups), an [incremental backup](take-full-and-incremental-backups.html#incremental-backups) will be appended to the full backup in a subdirectory. For example:

{% include copy-clipboard.html %}
~~~ sql
> BACKUP INTO LATEST IN
	  ('s3://us-east-bucket?COCKROACH_LOCALITY=default', 's3://us-west-bucket?COCKROACH_LOCALITY=region%3Dus-west');
~~~

{{site.data.alerts.callout_info}}
When [restoring from an incremental locality-aware backup](#restore-from-an-incremental-locality-aware-backup), you need to include _every_ locality ever used, even if it was only used once.

It is recommended that the same localities be included for every incremental backup in the series of backups; however, only the `default` locality is required.
{{site.data.alerts.end}}

And if you want to explicitly control where your incremental backups go, specify the subdirectory in the `BACKUP` statement:

{% include copy-clipboard.html %}
~~~ sql
> BACKUP INTO {subdirectory} IN
		('s3://us-east-bucket?COCKROACH_LOCALITY=default', 's3://us-west-bucket?COCKROACH_LOCALITY=region%3Dus-west');
~~~

To view the available subdirectories, use [`SHOW BACKUPS`](restore.html#view-the-backup-subdirectories).

## Restore from an incremental locality-aware backup

A locality-aware backup URI can also be used in place of any incremental backup URI in [`RESTORE`](restore.html).

For example, an incremental locality-aware backup created with

{% include copy-clipboard.html %}
~~~ sql
> BACKUP INTO LATEST IN
	  ('s3://us-east-bucket?COCKROACH_LOCALITY=default', 's3://us-west-bucket?COCKROACH_LOCALITY=region%3Dus-west')
~~~

can be restored by running:

{% include copy-clipboard.html %}
~~~ sql
> RESTORE FROM
  	('s3://us-east-bucket/{path/to/backup/subdirectory}', 's3://us-west-bucket/{path/to/backup/subdirectory}');
~~~

To view the available subdirectories, use [`SHOW BACKUPS`](restore.html#view-the-backup-subdirectories).

{{site.data.alerts.callout_info}}
When [restoring from an incremental locality-aware backup](take-and-restore-locality-aware-backups.html#restore-from-an-incremental-locality-aware-backup), you need to include _every_ locality ever used, even if it was only used once.
{{site.data.alerts.end}}

## Manually restore zone configurations from a locality-aware backup

During a [locality-aware restore](#restore-from-a-locality-aware-backup), some data may be temporarily located on another node before it is eventually relocated to the appropriate node. To avoid this, you need to manually restore [zone configurations](configure-replication-zones.html) first:

Once the locality-aware restore has started, [pause the restore](pause-job.html):

{% include copy-clipboard.html %}
~~~ sql
> PAUSE JOB 27536791415282;
~~~

The `system.zones` table stores your cluster's [zone configurations](configure-replication-zones.html), which will prevent the data from rebalancing. To restore them, you must restore the `system.zones` table into a new database because you cannot drop the existing `system.zones` table:

{% include copy-clipboard.html %}
~~~ sql
> RESTORE TABLE system.zones \
FROM 'azure://acme-co-backup?AZURE_ACCOUNT_KEY=hash&AZURE_ACCOUNT_NAME=acme-co' \
WITH into_db = 'newdb';
~~~

After it's restored into a new database, you can write the restored `zones` table data to the cluster's existing `system.zones` table:

{% include copy-clipboard.html %}
~~~ sql
> INSERT INTO system.zones SELECT * FROM newdb.zones;
~~~

Then drop the temporary table you created:

{% include copy-clipboard.html %}
~~~ sql
> DROP TABLE newdb.zones;
~~~

Then, [resume the restore](resume-job.html):

{% include copy-clipboard.html %}
~~~ sql
> RESUME JOB 27536791415282;
~~~

## See also

- [`BACKUP`](backup.html)
- [`RESTORE`](restore.html)
- [Take Full and Incremental Backups](take-full-and-incremental-backups.html)
- [Take and Restore Encrypted Backups](take-and-restore-encrypted-backups.html)
- [Take Backups with Revision History and Restore from a Point-in-time](take-backups-with-revision-history-and-restore-from-a-point-in-time.html)
- [`SQL DUMP`](cockroach-dump.html)
- [`IMPORT`](migration-overview.html)
- [Use the Built-in SQL Client](cockroach-sql.html)
- [Other Cockroach Commands](cockroach-commands.html)
