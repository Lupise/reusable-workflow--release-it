# Release-it versioning workflow

## Purpose

Manually callable workflow that try to create a release of the code:
- create a new version number from conventional commit compliant commits,
- Update changelog accordingly,
- create a git tag with the version,
- commit and push all this.

## How to use it

Create a .release-it.json file in your repository with the following content : 

```json
{
  "git": {
    "commitMessage": "Release ${version}",
    "requireBranch": "main",
    "requireUpstream": false,
    "requireCommits": true,
    "tagMatch": "[0-9]+\\.[0-9]+\\.[0-9]+"
  },
  "github": {
    "release": false
  },
  "npm": {
    "publish": false
  },
  "plugins": {
    "@release-it/conventional-changelog": {
      "infile": "CHANGELOG.md",
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

env:
  REGISTRY: ghcr.io
  GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

permissions:
  contents: write
  packages: write

jobs:
  manual-release:
    uses: Lupise/reusable-workflow--release-it/.github/workflows/versioning.yml@v1
    secrets: inherit
```

Commit and push all your new files

Generate an SSH key pairs:

```shell
ssh-keygen -t ed25519 -q -f ./id_rsa_my_repo -N ""  -C "git@github.com:Lupise/NAME_OF_THE_REPOSITORY.git"
```

Replace `NAME_OF_THE_REPOSITORY` with the name of the repository.

Add `./id_rsa_my_repo.pub` content as deploy key of the GitHub project with write permissions
Add `./id_rsa_my_repo` content as `RELEASE_IT_SSH_PRIVATE_KEY` in "release-it" environment secret.

After this, please delete the SSH key pairs:

```shell
rm ./id_rsa_reusable_workflows*
```

Needed configuration:

| Name                         | type   | Description                                                                       |
|------------------------------|--------|-----------------------------------------------------------------------------------|
| `RELEASE_IT_SSH_PRIVATE_KEY` | secret | SSH Private key allowed to write in the repository you want to generate releases. |
