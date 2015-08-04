# Table of Contents

* [Overview](#overview)
  * [Migration Scripts](#migration-scripts)
  * [Migration Script Best Practices](#migration-script-best-practices)
* [Level O: Scheduled Downtime](#level-0-scheduled-downtime)
* [Level 1: Scheduled Read-Only Mode](#level-1-scheduled-read-only-mode)
  * [In-Place Upgrade](#in-place-upgrade)
  * [Cross-Database Migration](#cross-database-migration)
* [Level 2: Zero Downtime Upgrades](#level-2-zero-downtime-upgrades)
  * [rm -rf Migration](#rm-rf-migration)
  * [Trivial Migration](#trivial-migration)
  * [Staged Migration](#staged-migration)
* [Notes](#notes)

# Overview

Services (and service development teams) are responsible for the integrity of their own data, and should perform data migrations in the manner is most useful to them while preserving their service’s SLA.

There is no “one size fits all” answer to data migration for services. Data migration strategies represent a continuum of impact, risk and effort, and teams are responsible for selecting the one most appropriate to the migration they are performing, and the service they are performing it on.

Data migration is not limited to modifying the schema of a relational database. For example, moving some (or all) of the service’s data to a different storage technology, or to be handled by a new service, is a data migration.

Changes that affect the serialization of data stored in shared caches such as memcache should also be treated as data migrations.

## Migration Scripts

Migration scripts are executables responsible for migrating the data from its old to new states. Ideally, a migration script should take the form of an executable that can be run by deploy tools, or scheduled as a task in Mesos.

It is not recommended that services perform migrations on service startup using frameworks built-in schema update tools, as these can be unpredictable and unsafe when run in a multi-service environment, and can disrupt the operation of other services. However, teams may wish to:

1. Package the automatic migration as a migration script through a custom build of the service, or a command line flag, so migrations can be more easily shared between dev and prod, or
2. (For migrations that can be performed without downtime) Build the migration into the service as an only-internally-accessible API, and have the migration script trigger that API on just one running node of the service at the appropriate time.

### Migration Script Best Practices

1. Where feasible, a migration script should verify the original data is valid before migrating it. Fix inconsistent or illegal data before the migration, not during or after it. (That way if something goes wrong and you have to roll back and perform the migration again, you have already fixed the data.)
2. Data migration scripts should be idempotent. If a migration dies halfway through, or if a migration completes successfully, but needs to be run again because a service has continued to write data in the old format, you should be able to just run the same script again to bring the new data up to date.
3. Data migrations should be tested as much as possible against copies of production data.
4. Rollback scripts are a nice idea, but keep in mind that they will be the least tested part of the upgrade, and unless they’re restoring from backup, probably utterly useless if a bug in the migration itself leaves the data in an inconsistent state.

([top](#table-of-contents))

# Data Migration Recipes

## Level O: Scheduled Downtime

1. Shut down the current version of the service
2. Migrate the data
3. Bring up the new version of the service

### When is this strategy appropriate?

When a service has a very lenient SLA. For example, the service is in development or beta and undergoing rapid development and frequent schema updates, and a more heavyweight process would slow down the development team unacceptably.

### What are the risks?

If the data migration fails, or deploying the new version of the service reveals critical bugs, rollback (or roll-forward) will be a labor-intensive manual process, and downtime will be extended significantly as a result.

([top](#table-of-contents))

## Level 1: Scheduled Read-Only Mode

### In-Place Upgrade

1. Bring the service down to read-only mode
2. Run the migration script
3. Perform rolling upgrade of service to new version (also in read-only mode)
4. Re-enable write mode

### Cross-Database Migration


1. Bring the service down to read-only mode
2. Copy the data to a new server, and run the migration script against that server
3. Perform rolling upgrade of service to new version pointing to new database
4. Re-enable write mode

### When is this strategy appropriate?

When the service has a lenient SLA, supports a read-only mode, and its database is small enough that making a copy is practical.

Note that for a service to support read-only mode, you also need to be sure that the service’s clients all handle write failures gracefully This makes this strategy much less useful for services with potentially large numbers of internal clients.

### What are the risks of this strategy?

As noted above, clients of the service may not handle read-only mode gracefully.

For an in-place upgrade, the same risks of downtime due to a bad upgrade apply. Cross-database migrations allow easy rollbacks during the migration, but once write mode is re-enabled, rolling back will require downtime.

## Level 2: Zero Downtime Upgrades

### rm -rf Migration

1. Provision a new, empty data source
2. Perform a rolling upgrade to the new version of the service, with the new version connected to that new data source
3. Decommission the old data source

#### When is this strategy appropriate?

When the data source is a cache that will be repopulated by the new version of the service, and there are no problematic cache races that will be caused by the two versions of the service running in parallel with partitioned caches.

#### What are the risks?

Cache races are usually problematic.

([top](#table-of-contents))

### Trivial Migration

1. Run the migration script
2. Perform a rolling upgrade to the new version of the service

#### When is this strategy appropriate?

When the data migration is 100% backwards compatible with the existing version of the service. For example, adding an unpopulated column or table to a relational database that the current version of the service will ignore.

#### What are the risks?

Data constraints (e.g. non-nullable columns and foreign keys in an RDBMS) that the old version of the service is unaware of may cause it to fail when inserting or updating data. One workaround is to only add the constraints after the rolling upgrade has been completed, but this introduces the risk of manual intervention being necessary if bad data was created while the constraints were not in place.

Your ability to roll back after the rolling upgrade is complete may be complicated once the new version of the service has started writing to the new columns/tables.

([top](#table-of-contents))

### Staged Migration

1. Perform a rolling upgrade to a version of the service that reads from the old data format, but can write to both old and new.
2. Run the migration script. Verify that the old and new data remain consistent.
3. Upgrade to a version of the service that reads the new version of the data, but writes to both old and new. Verify that the old and new data remain consistent.
4. Upgrade to a version of the service that reads and writes the new version of the data
5. Remove the no-longer-needed parts of the old data.

#### When is this strategy appropriate?

This is the preferred data migration strategy for services with tight SLAs, for any migration that is moderately risky, or for any migration that needs substantial verification in a live environment.

For verification, services may read from both old and new and log errors if they differ (while still returning whichever side they are configured to return).

Each service upgrade should be performed with canary deployments first in order to catch errors.

#### What are the risks?

Upgrading in this manner takes significant developer-time (and clock time), and can slow down a service’s pace of change significantly.

The migration is a complex, multi-stage process that will need to be QA’d at each step.

([top](#table-of-contents))

# Notes

Databases should be tagged with the version of their own schema in some way a service can query. Services should refuse to connect to a schema they do not recognise. Semantic versioning can help in situations where you want to support rolling back to the previous version.

