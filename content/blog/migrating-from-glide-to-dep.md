+++
title = "Migrating from glide to dep"
description = "My experience in migrating from glide to dep"
tags = [
    "golang", "go"
]
date = "2018-05-05"
categories = [
    "programming"
]
+++

I recently migrated one of the go lang projects that i contribute to which is [csvdiff](https://github.com/aswinkarthik93/csvdiff) from [glide](https://github.com/Masterminds/glide) to [dep](https://github.com/golang/dep). Here is what i had to do.

```bash
➜  csvdiff git:(glide-to-dep-migration) dep init
Importing configuration from glide. These are only initial constraints, and are further refined during the solve process.
Detected glide configuration files...
Converting from glide.yaml and glide.lock...
  Using ^1.0.0 as initial constraint for imported dep github.com/cespare/xxhash
  Trying v1.0.0 (5c37fe3) as initial lock for imported dep github.com/cespare/xxhash
  Trying master (b8bc1bf) as initial lock for imported dep github.com/mitchellh/go-homedir
  Using ^0.0.2 as initial constraint for imported dep github.com/spf13/cobra
  Trying v0.0.2 (a1f051b) as initial lock for imported dep github.com/spf13/cobra
  Using ^1.0.2 as initial constraint for imported dep github.com/spf13/viper
  Trying v1.0.2 (b5e8006) as initial lock for imported dep github.com/spf13/viper
  Using ^1.2.1 as initial constraint for imported dep github.com/stretchr/testify
  Trying v1.2.1 (12b6f73) as initial lock for imported dep github.com/stretchr/testify
  Trying v1.4.7 (c282820) as initial lock for imported dep github.com/fsnotify/fsnotify
  Trying master (ef8a98b) as initial lock for imported dep github.com/hashicorp/hcl
  Trying v1.0 (76626ae) as initial lock for imported dep github.com/inconshreveable/mousetrap
  Trying master (2c9e950) as initial lock for imported dep github.com/magiconair/properties
  Trying * (00c29f5) as initial lock for imported dep github.com/mitchellh/mapstructure
  Trying master (66540cf) as initial lock for imported dep github.com/pelletier/go-toml
  Trying v1.1.0 (6364489) as initial lock for imported dep github.com/spf13/afero
  Trying v1.2.0 (8965335) as initial lock for imported dep github.com/spf13/cast
  Trying master (7c0cea3) as initial lock for imported dep github.com/spf13/jwalterweatherman
  Trying v1.0.1 (583c0c0) as initial lock for imported dep github.com/spf13/pflag
  Trying * (f6cff07) as initial lock for imported dep golang.org/x/sys
  Trying * (7922cc4) as initial lock for imported dep golang.org/x/text
  Trying v2.2.1 (5420a8b) as initial lock for imported dep gopkg.in/yaml.v2
  Trying master (8991bc2) as initial lock for imported dep github.com/davecgh/go-spew
  Trying v1.0.0 (792786c) as initial lock for imported dep github.com/pmezard/go-difflib
  Using master as constraint for direct dep github.com/mitchellh/go-homedir
  Locking in master (b8bc1bf) for direct dep github.com/mitchellh/go-homedir
  Using ^1.0.0 as constraint for direct dep github.com/aswinkarthik93/csvdiff
  Locking in v1.0.0 (0b29a09) for direct dep github.com/aswinkarthik93/csvdiff
Old vendor backed up to /Users/kishanb/Programming/Tools/goworkspace/src/github.com/kishaningithub/csvdiff/_vendor-20180515104327
```

This created a couple of new files and a backup of the vendor directory.

```bash
➜  csvdiff git:(glide-to-dep-migration) ✗ git status
Alias tip: gst
On branch glide-to-dep-migration
Untracked files:
  (use "git add <file>..." to include in what will be committed)

	Gopkg.lock
	Gopkg.toml
	_vendor-20180515104327/

nothing added to commit but untracked files present (use "git add" to track)
```

Then i checked if the project builds successfully.

```bash
➜  csvdiff git:(glide-to-dep-migration) ✗ go test -race -coverprofile=coverage.txt -covermode=atomic -v ./...
?   	github.com/kishaningithub/csvdiff	[no test files]
=== RUN   TestPrimaryKeyPositions
--- PASS: TestPrimaryKeyPositions (0.00s)
=== RUN   TestValueColumnPositions
--- PASS: TestValueColumnPositions (0.00s)
=== RUN   TestConfigValidate
--- PASS: TestConfigValidate (0.00s)
=== RUN   TestDefaultConfigFormatter
--- PASS: TestDefaultConfigFormatter (0.00s)
=== RUN   TestConfigFormatter
--- PASS: TestConfigFormatter (0.00s)
=== RUN   TestJSONFormat
--- PASS: TestJSONFormat (0.00s)
=== RUN   TestRowMarkFormatter
--- PASS: TestRowMarkFormatter (0.00s)
PASS
coverage: 11.2% of statements
ok  	github.com/kishaningithub/csvdiff/cmd	1.042s	coverage: 11.2% of statements
=== RUN   TestDiff
--- PASS: TestDiff (0.00s)
=== RUN   TestCreateDigest
--- PASS: TestCreateDigest (0.00s)
=== RUN   TestDigestForFile
--- PASS: TestDigestForFile (0.00s)
=== RUN   TestPositionsMapValues
--- PASS: TestPositionsMapValues (0.00s)
=== RUN   TestPositionsMapValuesReturnsCompleteStringCsvIfEmpty
--- PASS: TestPositionsMapValuesReturnsCompleteStringCsvIfEmpty (0.00s)
PASS
coverage: 0.0% of statements
ok  	github.com/kishaningithub/csvdiff/pkg/digest	1.031s	coverage: 0.0% of statements
```

Since it did i removed the glide.* files and the backed up _vendor-* directory.

This is the [pull request](https://github.com/aswinkarthik93/csvdiff/pull/12) submitted to [that repo](https://github.com/aswinkarthik93/csvdiff) for this change.

That's all folks!