+++
title = "Aws cli by example"
description = "Documentation of my experiments with aws cli"
date = 2018-11-26T15:37:58+01:00
tags = ["aws","cli"]
categories = []
+++

## Dynamo DB

### Get value using primary key

```bash
aws dynamodb get-item --table-name users --key '{"username": {"S": "test"}}'
```

### Dump table data as csv

This expects that you have installed [jq command](https://github.com/stedolan/jq) on you system.

```bash
aws dynamodb scan --table-name users \
  --query "Items[].[username.S,email.S,passwordHash.S]" \
  --output json | jq -r '.[] | @csv' > dump.csv
```

### Query using a portion of composite key

```bash
  aws dynamodb query --table-name users \
   --key-condition-expression "username = :username" \
   --expression-attribute-values  '{":username":{"S":"test user"}}'
```