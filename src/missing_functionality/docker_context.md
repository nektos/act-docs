# `docker context` support

The current `docker context` isn't respected ([#583](https://github.com/nektos/act/issues/583)).

You can work around this by setting `DOCKER_HOST` before running `act`, with e.g:

```bash
export DOCKER_HOST=$(docker context inspect --format '{{.Endpoints.docker.Host}}')
```

If your other host requires client certificates, the `DOCKER_CERT_PATH` environment variable *is* respected.

```bash
export DOCKER_CERT_PATH=$(docker context inspect --format '{{.Storage.TLSPath}}')/docker
```
