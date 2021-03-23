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
brew bundle
```