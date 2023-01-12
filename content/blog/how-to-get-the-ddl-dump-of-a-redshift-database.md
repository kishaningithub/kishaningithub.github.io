+++
title = "How to get the DDL dump of a redshift database"
description = ""
date = 2023-01-12T10:34:53+05:30
tags = ["aws", "redshift", "redshift serverless"]
categories = []
+++

When I was googling "How to get the DDL dump of a redshift database" I found [this blog](https://awsbytes.com/how-to-get-the-ddl-of-a-table-in-redshift-database/)
which proposed a way do it but the con of the approach proposed there is it requires us create new objects in redshift just to extract DDL which was not acceptable
for me, so I tried and found a better way which is given below

## Approach

The approach below makes use of the following commands from redshift and wraps them in a bash script which makes use of the redshift data api
- [SHOW TABLE](https://docs.aws.amazon.com/redshift/latest/dg/r_SHOW_TABLE.html)
- [SHOW VIEW](https://docs.aws.amazon.com/redshift/latest/dg/r_SHOW_VIEW.html)
- [SHOW_EXTERNAL_TABLE](https://docs.aws.amazon.com/redshift/latest/dg/r_SHOW_EXTERNAL_TABLE.html)

## Steps

- Ensure [awscli](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) and [jq](https://stedolan.github.io/jq/tutorial/) command are installed.
- Create a script named `dumpddl.sh` with the following contents and give it executable permissions using command `chmod +x ./dumpddl.sh`
```bash
#!/usr/bin/env bash

CLUSTER_IDENTIFIER="<<your cluster identifier - required only for managed redshift>>"
WORKGROUP_NAME="<<workgroup name - required only for serverless>>"
DATABASE="<<your database>>"
DATABASE_USER="<<your database user>>"

function query_result() {
  query="$1"
  query_id=$(aws redshift-data execute-statement --with-event --db-user "${DATABASE_USER}" --cluster-identifier "${CLUSTER_IDENTIFIER}" --workgroup-name "${WORKGROUP_NAME}" --database "${DATABASE}" --sql "${query}" | jq -r ".Id")
  sleep 2
  aws redshift-data get-statement-result --id "${query_id}" | jq -r ".Records[][0].stringValue"
  echo ""
}

# Replace the below names of tables, views and external tables with the ones you want to take ddl dump on
query_result "SHOW TABLE person"
query_result "SHOW VIEW org_view"
query_result "SHOW EXTERNAL TABLE \"org_schema\".employee_details"
```
- Run following command to get the ddl dump
```bash
./dumpddl.sh > ddl_dump.sql
```

## Pros
- No need to install anything inside the redshift cluster.
- Works from the local machine even if redshift cluster is within a VPC(through redshift data API).
- Works for both managed redshift and serverless redshift.

## Cons
- Requires us to know the list of tables and views for which we want the ddl dump.
