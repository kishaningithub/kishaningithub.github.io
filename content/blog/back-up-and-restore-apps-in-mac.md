+++
title = "Backup and restore all your apps in Mac OS"
description = "Backup and restore all your apps in Mac OS"
date = 2021-03-23T10:24:15+05:30
tags = ["macosx","mac"]
categories = []
+++

## Prerequisite

This assumes you are installing everything using [homebrew](https://brew.sh/) as a practice.
If you are not doing it already, getting started is as easy as it gets. Some

Search for an app(cli/gui)
```bash
brew search <<app>>
```
To install a cli app
```bash
brew install <<cli app>>
```
To install a GUI app
```bash
brew install --cask <<gui app>>
```

## Backup all your apps (cli and gui)
```bash
brew bundle dump
```

This will output a file called `Brewfile` whose contents will look like the following

```
tap "dkanejs/aws-session-manager-plugin"
tap "go-delve/delve"
tap "goreleaser/tap"
brew "wget"
brew "youtube-dl"
cask "gimp"
cask "zoom"
```

You can ship this file anywhere

## Restore all your apps (cli and gui)

Go to the directory in which you have your `Brewfile` and then run
```bash
brew bundle dump
```


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

## KMS

### Generate data key from KMS master key

```bash
aws kms generate-data-key-without-plaintext --key-id <<KMS master key>> --key-spec AES_256 --query CiphertextBlob --output text
```