# Beginners guide

## Events

- `act` has basic event support, it will try to get as much information from local repository as possible, although this might be not enough for certain event types.
- Running `act` without any event name specified will run with event `push`.
- For a list of event names, please see [events that trigger workflows](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows "events-that-trigger-workflows").

---

- `push`:

```shell=sh
act push
```

> Runs all workflows with `push` event

- `pull_request`:

```shell=sh
act pull_request
```

- `schedule`:

```shell=sh
act schedule
```

To list all workflows for a given event, use `-l`/`--list`:

```shell=sh
act -l pull_request
```

### Using event file to provide complete event payload

If your workflow relies on passed event properties, you will have to provide required properties in the event file, example:

> To partially simulate `pull_request` event, you to provide at least `head_ref` and `base_ref`. This values can be later accessed via `${{ github.event.pull_request.<...> }}`

```json
{
  "pull_request": {
    "head": {
      "ref": "sample-head-ref"
    },
    "base": {
      "ref": "sample-base-ref"
    }
  }
}
```

> To partially simulate `push` event with a tag, you need to provide `ref` which will be accessible via `${{ github.event.ref }}`

```json
{
  "ref": "refs/tags/this-is-a-tag"
}
```

## Workflows

> By default `act` will run **all** workflows in `.github/workflows`.

You can override that behaviour with `-W`/`--workflows` flag by specifying directory containing workflow files

```shell=sh
act -W '.github/workflows/'
```

> This example will run **all jobs** in **all workflows** in directory `.github/workflows` but only if the trigger event is `push`

---

or by specifying exact workflow file to run

```shell=sh
act -W '.github/workflows/checks.yml'
```

> This example will run **all jobs** in `.github/workflows/checks.yml` workflow file but only if it's trigger event is `push`

## Jobs

> By default `act` will run **all jobs** in **all workflows** that are triggerred by `push` event

```shell=sh
act -j 'test'
```

> This example will run **all jobs** named `test` in **all workflows** that trigger on `push` event

## Secrets

To run `act` with secrets, you can enter them interactively, supply them as environment variables or load them from a file. The following options are available for providing secrets:

---

## **⚠ WARNING ⚠**

When inserting sensitive data in your terminal, it might be saved as plain text to history file provided by your shell. To mitigate that, prefix `act ...` command with a space (not all shells respect that) or use secure input (explained below) to insert data.

---

- `act -s MY_SECRET=somevalue` - use `somevalue` as the value for `MY_SECRET`.
- `act -s MY_SECRET` - check for an environment variable named `MY_SECRET` and use it if it exists. If the environment variable is not defined, prompt the user for a value. **This is recommended way of typing/pasting a secret into terminal, as `act` will provide secure input prompt for you to type/paste your secret which will not be saved in your shell history file.**
- `act --secret-file my.secrets` - load secrets values from `my.secrets` file.
  - secrets file format is the same as `.env` format

### `GITHUB_TOKEN`

GitHub Actions provides `secrets.GITHUB_TOKEN` and `github.token` automatically, on which many actions rely. This is possible to do so in `act`, if you use set it as a secret (`GITHUB_TOKEN=ghp_...`).
If your workflow fails with an error about `token`, it most likely requires `GITHUB_TOKEN` to be set up.

## `.env`/`.secrets` files structure

`.env` and `.secrets` files are using Ruby's gem [`dotenv`](https://github.com/bkeepers/dotenv) format through [`godotenv`](https://github.com/joho/godotenv) library

Example:

```shell
export MY_ENV='value'
PRIV_KEY="---...\nrandom text\n...---"
JSON="{\n\"name\": \"value\"\n}"
SOME_VAR=SOME_VALUE
```

To see more examples, go to <https://github.com/joho/godotenv/tree/v1.4.0/fixtures>

## Skipping jobs

You cannot use the `env` context in job level if conditions, but you can add a custom event property to the `github` context. You can use this method also on step level if conditions.

```yml
on: push
jobs:
  deploy:
    if: ${{ !github.event.act }} # skip during local actions testing
    runs-on: ubuntu-latest
    steps:
    - run: exit 0
```

And use this `event.json` file with act otherwise the Job will run:

```json
{
    "act": true
}
```

Run act like

```sh
act -e event.json
```

_Hint: you can add / append `-e event.json` as a line into `./.actrc`_

## Skipping steps

Act adds a special environment variable `ACT` that can be used to skip a step that you
don't want to run locally. E.g. a step that posts a Slack message or bumps a version number.
**You cannot use this method in job level if conditions, see [Skipping jobs](#skipping-jobs)**

```yml
- name: Some step
  if: ${{ !env.ACT }}
  run: |
    ...
```

## Pass Inputs to Manually Triggered Workflows

Example workflow file

```yaml
on:
  workflow_dispatch:
    inputs:
      NAME:
        description: "A random input name for the workflow"
        type: string
      SOME_VALUE:
        description: "Some other input to pass"
        type: string
jobs:
  test:
    name: Test
    runs-on: ubuntu-latest
    steps:
      - name: Test with inputs
        run: |
          echo "Hello ${{ github.event.inputs.NAME }} and ${{ github.event.inputs.SOME_VALUE }}!"
```

### via input or input-file flag

- `act --input NAME=somevalue` - use `somevalue` as the value for `NAME` input.
- `act --input-file my.input` - load input values from `my.input` file.
  - input file format is the same as `.env` format

### via JSON

Example JSON payload file conveniently named `payload.json`

```json
{
  "inputs": {
    "NAME": "Manual Workflow",
    "SOME_VALUE": "ABC"
  }
}
```

Command for triggering the workflow

```sh
act workflow_dispatch -e payload.json
```
