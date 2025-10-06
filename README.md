# Lagoss Action

Easily integrate [Lagoss](https://lagoss.com) CLI into your Github workflows. Deploy new applications, retrieve a list of existing applications, promote deployments, etc. This action supports any [arbitrary input](#other-commands) of what to do!

## Usage

Create the following workflow in your application's repository:

_./github/workflows/lagoss.yml_

```yml
name: Lagoss

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    name: Deploy
    steps:
      - uses: actions/checkout@v2
      - uses: lagossapp/github-action@latest
        with:
          lagoss_token: ${{ secrets.LAGOSS_TOKEN }}
      - name: Log deployment URL
        run: |
          url=$(grep -o 'https://[^[:space:]]*' lagoss.output)
          echo "Deployment available at: $url"
```

This will deploy your source code to the specified application after a commit is pushed into main.

_NOTE: Make sure the repository that gets checked out contains a `.lagoss/config.json` file that specifies information such as the application_id and organization_id or pass in a config via the config input value mentioned below!_

#### Other commands

If you want to run a different command just specify it with the `command` input:

```bash
        with:
          lagoss_token: ${{ secrets.LAGOSS_TOKEN }}
          command: ls
```

See [CLI](https://docs.lagoss.app/cli) docs for more commands.

## Inputs

Inputs are provided using the `with:` section of your workflow YML file.

| key         | Description                  | Required | Default                |
| ----------- | ---------------------------- | -------- | ---------------------- |
| lagoss_token | Your Lagoss API token         | true     |                        |
| command     | The Lagoss CLI command to run | false    | deploy --prod          |
| site_url    | Specify Lagoss API domain     | false    | https://dash.lagoss.app |
| config      | Config file for application     | false    |                        |

`site_url` is used to specify a custom endpoint if you are using a self-hosted instance of Lagoss.

`config` allows you to override a repositories existing config or maybe it never existed because you didn't want to commit it:

```bash
        with:
          lagoss_token: ${{ secrets.LAGOSS_TOKEN }}
          config: |
            {
              "application_id": "${{ vars.lagoss_application_id }}",
              "organization_id": "${{ vars.lagoss_org_id }}",
              "index": "hello.ts",
              "client": null,
              "assets": null
            }
```

This example is setting the application and org ID with [variables](https://docs.github.com/en/actions/learn-github-actions/variables#creating-configuration-variables-for-a-repository).

## Outputs

Since the action allows you to run any command, there are no outputs. Instead, the CLI stdout will be saved to a local file called `lagoss.output`.
