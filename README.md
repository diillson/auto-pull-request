# GitHub Pull Request

<!-- ALL-CONTRIBUTORS-BADGE:START - Do not remove or modify this section -->
[![All Contributors](https://img.shields.io/badge/all_contributors-19-orange.svg?style=flat-square)](#contributors-)
<!-- ALL-CONTRIBUTORS-BADGE:END -->

A GitHub Action for creating pull requests.


## Features
 * Create pull requests
 * Create pull requests on other repos
 * Add reviewers, assignees, labels, or milestones
 * Customize pull request title and body
 * Retrieve the existing pull request url if a pull request already exists


## Usage

### GitHub Actions
```yaml
# File: .github/workflows/pull-request.yml

on:
  push:
    branches:
    - develop

jobs:
  pull-request:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - name: pull-request
    uses: diillson/auto-pull-request@v1.0.1
      with:
        destination_branch: "main"
```

This will automatically create a pull request from `develop` to `main`.


## Advanced options
```yaml
on:
  push:
    branches:
    - "feature*"  # Support wildcard matching

jobs:
  pull-request:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - name: pull-request
    uses: diillson/auto-pull-request@v1.0.1
      with:
        source_branch: ""                                 # If blank, default: triggered branch
        destination_branch: "main"                      # If blank, default: master
        pr_title: "Pulling ${{ github.ref }} into master" # Title of pull request
        pr_body: |                                        # Full markdown support, requires pr_title to be set
          :crown: *An automated PR*

_Created by [diillson/auto-pull-request](https://github.com/diillson/auto-pull-request)_
        pr_template: ".github/PULL_REQUEST_TEMPLATE.md"   # Path to pull request template, requires pr_title to be set, excludes pr_body
        pr_reviewer: "diillson,worker"                         # Comma-separated list (no spaces)
        pr_assignee: "diillson,worker"                         # Comma-separated list (no spaces)
        pr_label: "auto-pr"                               # Comma-separated list (no spaces)
        pr_milestone: "Milestone 1"                       # Milestone name no Required it is comment
        pr_draft: true                                    # Creates pull request as draft
        pr_allow_empty: true                              # Creates pull request even if there are no changes
        github_token: ${{ secrets.CUSTOM_GH_TOKEN }}      # If blank, default: secrets.GITHUB_TOKEN
```

### Third-party repositories

Since it's possible to `checkout` third-party repositories, you can either define `destination_repository` manually or let
this action automatically pick up the checked out repository.

```yaml
jobs:
  pull-request:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
      with:
        repository: "octocat/hello-world"
    - name: pull-request
    uses: diillson/auto-pull-request@v1.0.1
      with:
        destination_branch: "main"
        github_token: ${{ secrets.GITHUB_TOKEN }}
      # destination_repository: "octocat/hello-world" <- You can also do this but not necessary
```

**Priority will be set as follows:**

1. `destination_repository` (Manually set)
2. Checked out repository
3. Repository that triggered the action (`GITHUB_REPOSITORY`)

### Outputs

The following outputs are available: `pr_url`, `pr_number`, `pr_created ("true"|"false")`, `has_changed_files ("true"|"false")`.

```yaml
on:
  push:
    branches:
    - develop

jobs:
  pull-request:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - name: pull-request
      id: open-pr
      uses: diillson/auto-pull-request@v1.0.1
      with:
        destination_branch: "main"
        github_token: ${{ secrets.GITHUB_TOKEN }}
    - name: output-url
      run: echo ${{steps.open-pr.outputs.pr_url}}
    - name: output-number
      run: echo ${{steps.open-pr.outputs.pr_number}}
    - name: output-created
      run: echo ${{steps.open-pr.outputs.pr_created}}
    - name: output-has-changed-files
      run: echo ${{steps.open-pr.outputs.has_changed_files}}

```

## Docker Container Image Usage

Sometimes you might want to use a pre-built container image directly. This could result in faster runs and prevent needlessly rebuilding container images over-and-over on self-hosted runners.

```yml
on:
  push:
    branches:
    - develop

jobs:
  pull-request:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: pull-request
    uses: docker://public.ecr.aws/y2v3z0w4/pullrequest:v1
      with:
        destination_branch: "main"
        github_token: ${{ secrets.GITHUB_TOKEN }}         # Required to use container image
```
