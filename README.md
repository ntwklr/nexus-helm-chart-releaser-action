# *chart-releaser* Action

A GitHub action to push Helm Chart to Sonatype Nexus Repository Manager
, using [helm/chart-releaser](https://github.com/helm/chart-releaser) CLI tool.

## Usage

### Pre-requisites

1. A GitHub repo containing a directory with your Helm charts (eg: `/charts`)
2. A "hosted" Helm Repository on your Nexus Repository installation.
3. Create a workflow `.yml` file in your `.github/workflows` directory. An [example workflow](#example-workflow) is available below.
  For more information, reference the GitHub Help Documentation for [Creating a workflow file](https://help.github.com/en/articles/configuring-a-workflow#creating-a-workflow-file)

### Inputs

- `version`: The chart-releaser version to use (default: v1.2.1)
- `config`: Optional config file for chart-releaser. For more information on the config file, see the [documentation](https://github.com/helm/chart-releaser#config-file)
- `charts_dir`: The charts directory
- `charts_repo_url`: The Repository URL to the charts repo (example:  `https://example.com/repository/repo/`)
- `username`: Username to connect to Nexus to publish components
- `password`: Password to connect to Nexus to publish components

### Environment variables

- `CR_TOKEN` (required): The GitHub token of this repository (`${{ secrets.GITHUB_TOKEN }}`)

For more information on environment variables, see the [documentation](https://github.com/helm/chart-releaser#environment-variables).

### Example Workflow

Create a workflow (eg: `.github/workflows/release.yml`):

```yaml
name: Release Charts

on:
  push:
    branches:
      - main

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2
        with:
          fetch-depth: 0

      - name: Configure Git
        run: |
          git config user.name "$GITHUB_ACTOR"
          git config user.email "$GITHUB_ACTOR@users.noreply.github.com"

      - name: Install Helm
        uses: azure/setup-helm@v1
        with:
          version: v3.4.0

      - name: Run chart-releaser
        uses: ntwklr/nexus-helm-chart-releaser-action@v0.0.1
        with:
          charts_repo_url: https://example.com/repository/repo/
          username: "${{ secrets.NEXUS_USERNAME }}"
          passowr: "${{ secrets.NEXUS_PASSWORD }}"
        env:
          CR_TOKEN: "${{ secrets.GITHUB_TOKEN }}"
```

This uses [@ntwklr/nexus-helm-chart-releaser-action](https://www.github.com/ntwklr/nexus-helm-chart-releaser-action) to push Helm Chart to Sonatype Nexus Repository Manager
It does this – during every push to `main` – by checking each chart in your project, and whenever there's a new chart version, pushes the Helm chart artifact to the configured Nexus Repository.

#### Example using custom config

`workflow.yml`:
```yaml
- name: Run chart-releaser
  uses: helm/chart-releaser-action@v1.2.0
  with:
    charts_dir: charts
    config: cr.yaml
    charts_repo_url: https://example.com/repository/repo/
    username: "${{ secrets.NEXUS_USERNAME }}"
    passowr: "${{ secrets.NEXUS_PASSWORD }}"
  env:
    CR_TOKEN: "${{ secrets.GITHUB_TOKEN }}"
```

`cr.yaml`:
```yaml
owner: myaccount
git-base-url: https://api.github.com/
```

For options see [config-file](https://github.com/helm/chart-releaser#config-file). 

## Code of conduct

Participation in the Helm community is governed by the [Code of Conduct](CODE_OF_CONDUCT.md).
