# Release-it versioning workflow

## Purpose

Manually callable workflow that try to create a release of the code:
- create a new version number from conventional commit compliant commits,
- create a git tag with the version,
- create a GitHub release,
- move the major tag to the last version

## How to use it

Create a .release-it.json file in your repository with the following content : 

```json
{
  "git": {
    "commitMessage": "Release ${version}",
    "requireBranch": "main",
    "requireUpstream": false,
    "requireCommits": true,
    "tagExclude": "?"
  },
  "github": {
    "release": true
  },
  "npm": {
    "publish": false
  },
  "plugins": {
    "@release-it/conventional-changelog": {
      "header": "# REPOSITORY_NAME",
      "preset": {
        "name": "conventionalcommits",
        "types": [
          {
            "type": "feat",
            "section": "Features"
          },
          {
            "type": "fix",
            "section": "Bug Fixes"
          },
          {
            "type": "chore",
            "section": "Miscellaneous"
          },
          {
            "type": "docs",
            "section": "Miscellaneous"
          },
          {
            "type": "style",
            "section": "Miscellaneous"
          },
          {
            "type": "refactor",
            "section": "Miscellaneous"
          },
          {
            "type": "perf",
            "section": "Miscellaneous"
          },
          {
            "type": "test",
            "section": "Miscellaneous"
          },
          {
            "type": "ci",
            "section": "Miscellaneous"
          },
          {
            "type": "build",
            "section": "Miscellaneous"
          }
        ]
      }
    }
  }
}

```

Create a file called `.github/workflows/versioning.yml` in your repository with the following content: 

```yaml
name: Versioning and releasing

on: [workflow_dispatch]

permissions:
  contents: write

jobs:
  release:
    uses: Lupise/reusable-workflow--release-it/.github/workflows/versioning.yml@v3
    secrets: inherit
```

if you need to trigger another workflow after the release, you can use `versioning_and_build.yml` workflow, for example::

```yaml
name: Versioning and releasing

on:
  workflow_dispatch:
  push:
    branches:
      - main
    paths: [
      'src/**',
      'Dockerfile'
    ]

permissions:
  contents: write
  packages: write
  actions: write

jobs:
  manual-release:
    uses: Lupise/reusable-workflow--release-it/.github/workflows/versioning_and_build.yml@v2
    secrets: inherit
    with:
      NEXT_WORKFLOW_NAME: build.yml
```

Commit and push all your new files.
