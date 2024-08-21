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
    git clone --depth 1 -b "${{ github.head_ref || github.ref_name }}" "https://github.com/${{ github.repository }}.git" .
```

### Note

- `--depth 1` performs a shallow-clone there by increasing your performance as we don't need to pull the entire commit history for every pipeline run.
- `${{ github.head_ref || github.ref_name }}` doing this to ensure we always get the name of the branch which triggered the pipelines. This way of doing it makes it trigger agnostic i.e. both push and pull request trigger will work as intended.
- `.` The dot at the end ensures that the checkout happens in the current directory which is the workspace.