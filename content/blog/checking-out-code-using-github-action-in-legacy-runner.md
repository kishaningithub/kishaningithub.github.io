+++
title = "Checking Out Code Using Github Action in Legacy Runner"
description = ""
date = 2024-08-18T13:31:29+05:30
tags = []
categories = []
+++

## Problem

Performing a simple git checkout using official [checkout](https://github.com/actions/checkout) action fails in a legacy self hosted runner running amazon linux 2 because newer versions of nodejs(>= 20) requires a newer version of GLIBC which is not available in these operating systems.

## Solution

Use bash to perform the checkout in the action code i.e

replace the line

```yaml
- uses: actions/checkout@v3
```

with

```yaml
- name: Checkout
  run: |
    git clone "https://github.com/${GITHUB_REPOSITORY}.git" "${GITHUB_WORKSPACE}"
```