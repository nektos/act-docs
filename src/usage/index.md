# Usage guide

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

If your workflow relies on passed event properties, you will have to provide the required properties in the event file, example:

> To partially simulate `pull_request` event, you have to provide at least `head_ref` and `base_ref`. This values can be later accessed via `${{ github.event.pull_request.<...> }}`

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

You can override that behaviour with `-W`/`--workflows` flag by specifying the directory containing workflow files.

```shell=sh
act -W '.github/workflows/'
```

> This example will run **all jobs** in **all workflows** in the directory `.github/workflows` but only if the trigger event is `push`

---

or by specifying the exact workflow file to run

```shell=sh
act -W '.github/workflows/checks.yml'
```

> This example will run **all jobs** in `.github/workflows/checks.yml` workflow file but only if its trigger event is `push`

## Jobs

> By default `act` will run **all jobs** in **all workflows** that are triggerred by `push` event

```shell=sh
act -j 'test'
```

> This example will run **all jobs** named `test` in **all workflows** that trigger on `push` event

## Configuration file

Act can be configured using `.actrc` files. All found arguments will be parsed and appended to a list, in order of: .actrc as per the XDG spec, .actrc in HOME directory, .actrc in invocation directory, cli arguments.

Format: One argument per line, no comments supported.

Example:

```
--container-architecture=linux/amd64
--action-offline-mode
```

# Vars

To run `act` with repository variables that are accessible inside the workflow via `${{ vars.VARIABLE }}`, you can enter them interactively or load them from a file. The following options are available for providing github repository variables:

- `act --var VARIABLE=somevalue` - use `somevalue` as the value for `VARIABLE`.
- `act --var-file my.variables` - load variables values from `my.variables` file.
- The variables file format is the same as `.env` format

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

GitHub [automatically provides](https://docs.github.com/en/actions/security-guides/automatic-token-authentication#about-the-github_token-secret) a `GITHUB_TOKEN` secret when running workflows inside GitHub. If your workflow fails with an error about `token`, it most likely requires `GITHUB_TOKEN` to be set up.
If your workflow depends on this token, you need to create a [personal access token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token) and pass it to `act` as a secret:
```bash
act -s GITHUB_TOKEN=[insert token or leave blank and omit equals for secure input]
```

If [GitHub CLI](https://cli.github.com/) is installed, the [`gh auth token`](https://cli.github.com/manual/gh_auth_token) command can be used to automatically pass the token to act

```bash
act -s GITHUB_TOKEN="$(gh auth token)"
```

**WARNING**: `GITHUB_TOKEN` will be logged in shell history if not inserted through secure input or (depending on your shell config) the command is prefixed with a whitespace.

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

## Specifying Matrix

You can selectively choose a subset of matrix options to run by specifying the `--matrix` flag. It will only run those matrix configurations
which include your specified values.

Example workflow file

```yaml
name: matrix-with-user-inclusions
on: push
jobs:
  build:
    name: Matrix
    runs-on: ubuntu-latest
    steps:
      - run: echo ${NODE_VERSION}
        env:
          NODE_VERSION: ${{ matrix.node }}
    strategy:
      matrix:
        os: [ubuntu-18.04, macos-latest]
        node: [4, 6, 8, 10]
        exclude:
          - os: macos-latest
            node: 4
        include:
          - os: ubuntu-16.04
            node: 10
```

In this case if we only wanted to run this workflow for node 8, then we would run `act push --matrix node:8`

This will trigger the workflow to use the following matrix configurations only:
- `os: ubuntu-18.04, node: 8`
- `os: macos-latest, node: 8`

Similarly if we just wanted to trigger this workflow for node 10 and macos-latest then we would run `act push --matrix node:10 --matrix os:macos-latest`.

This will trigger the workflow to use the following matrix configurations only:
- `os: macos-latest, node 10`

Note that using the `--matrix` flag you can't add new values (for e.g. running the above workflow for node 20). It will simply ignore it. Moreover, the `exclude` field in the workflow will take precedence over the `--matrix` flag (for e.g. running the above workflow for only macos-latest and node 4 will result in no matrix configuration being used)

## Action Offline Mode

If you want to speed up running act and using cached actions and container images you can enable this mode.

- stops pulling existing images
- stops failing if an action has been cached and you cannot connect to GitHub
- pulls nonexistent actions and images
- act will work offline if it has at least ran once while you are online
- get rid of unnecessary timeouts when you have an unstable connection to GitHub or Container registries
- workaround rate limit problems

```sh
  act --action-offline-mode
```

or a `.actrc` file in your cwd like
```
--action-offline-mode
```

## Action Artifacts

**Currently the artifacts server is not started automatically with act**, this means the following env variables are blank by default.

- `ACTIONS_RUNTIME_URL`
- `ACTIONS_RUNTIME_TOKEN`
- `ACTIONS_RESULTS_URL`

to enable this feature use the cli flag `--artifact-server-path $PWD/.artifacts`.

While enabled these values are also available in `run` steps, which doesn't match `actions/runner` aka GitHub Actions where their are blank.

Currently `actions/upload-artifact@v3` and `actions/upload-artifact@v4` together with `actions/download-artifact@v3` and `actions/download-artifact@v4` should be able to upload and download their artifacts within the current workflow run.

Not supported v4 features are to download artifacts from a different run, workflow or repository by providing a GitHub Token.
