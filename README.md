# Multipass Workflows
This repository contains multipass workflow definitions. They augment the offerings already available from the
[Ubuntu Cloud Images](http://cloud-images.ubuntu.com/). You can list the available images with
[`multipass find`](https://multipass.run/docs/find-command) and run them with [`multipass launch`](https://multipass.run/docs/launch-command):

```plain
$ multipass find
Image                       Aliases           Version          Description
# ...
minikube                                      latest           minikube is local Kubernetes

$ multipass launch minikube
Launched: minikube

$ multipass exec minikube -- minikube status
minikube
type: Control Plane
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
```

## Schema
The workflows are defined in YAML of the following format (required fields marked with `*`):
```yaml
# v1/<name>.yaml

description: <string>      # * a short description of the workflow ("tagline")
version: <string>          # * a version string

runs-on:                   # a list of architectures this workflow can run on
- arm64                    #   see https://doc.qt.io/qt-5/qsysinfo.html#currentCpuArchitecture
- x86_64                   #   for a list of valid values

instances:
  <name>:                  # * equal to the workflow name
    image: <base image>    # a valid image alias, see `multipass find` for available values
    limits:
      min-cpu: <int>       # the minimum number of CPUs this workflow can work with
      min-mem: <string>    # the minimum amount of memory (can use G/K/M/B suffixes)
      min-disk: <string>   # and the minimum disk size (as above)
    timeout: <int>         # maximum time for the instance to launch, and separately for cloud-init to complete
    cloud-init:
      vendor-data: |       # cloud-init vendor data
        <string>
```

## Testing
On Linux, the `multipass find` command looks for workflows in a URL provided by an
environment variable, `MULTIPASS_WORKFLOWS_URL`. To locally test your workflows
you would need to override the systemd service with the following setting:

```conf
[Service]
Environment="MULTIPASS_WORKFLOWS_URL=https://github.com/USERNAME/multipass-workflows/archive/refs/heads/BRANCH_NAME.zip"
```

This can be done by using the `systemctl edit` utility:

```shell
sudo systemctl edit snap.multipass.multipassd.service
```

followed by service restart:

```shell
sudo snap restart multipass
```
