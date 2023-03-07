# Unsupported functionality

While we try to implement all features of GitHub Runners, due to design choices and manpower available it's not possible to make `act` completely compatible.  

Here is a list of features that is (yet) to be implemented or is decided as not going to be worked on by `act` maintainers.

## Planned

- Services are not supported ([#173](https://github.com/nektos/act/issues/173))
- `concurrency` is ignored
- `run-name` is ignored
- Context availability is not checked, so you can use the env context on more places
- Step summary not processed
- Problem matcher ignored
- Annotations ignored
- No `vars` context
- Incomplete `github` context
- Run steps cancellation not implemented
- `job.permissions` ignored
- `timeout-minutes` ignored
- `job.continue-on-error` ignored
- `PATH` of container / of act must contain node for nodejs actions, github runner has their own copy for both container and host
- Openid Connect url is not defined
- `job.environment` ignored and scoping secrets by deployment environment is not supported

## Functionality that is not going to be worked on

- [Docker context](./missing_functionality/docker_context.md) ([#583](https://github.com/nektos/act/issues/583))
