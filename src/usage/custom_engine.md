# Custom container engine

You can use a different container engine that is compatible with the Docker Engine API, by setting the `DOCKER_HOST` environment variable to a custom socket path.

## Example how to use `podman` with `act` using `sh` shell

```shell
DOCKER_HOST='unix:///var/run/podman/podman.sock' act # ...
```

or

```shell
export DOCKER_HOST='unix:///var/run/podman/podman.sock'
act # ...
```

## Using `act` with remote Docker engine via SSH

```shell
DOCKER_HOST='ssh://user@host' act # ...
```

## Using `act` with remote Docker engine via SSH in PowerShell

```powershell
$env:DOCKER_HOST = 'ssh://user@host'
act # ...
```
