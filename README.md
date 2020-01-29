# Drone Rsync Plugin
This fork is a mirror of [Rsync Plugin](https://github.com/Drillster/drone-rsync) for [Drone](https://drone.io/) that simply uses `rsync` to deploy a set of files to a remote directory on a server. This fork exists as a level of indirection in case the original plugin disappears (since it is a comminity Drone plugin).

## Usage

Use the plugin to synchronize files to remote hosts, and execute arbitrary commands on those hosts.

### Config
The following parameters are used to configure the plugin:
- **user** - user to log in as on the remote machines, defaults to `root`
- **key** - private SSH key for the remote machines
- **hosts** - hostnames or ip-addresses of the remote machines
- **port** - port to connect to on the remote machines, defaults to `22`
- **source** - source folder to synchronize from, defaults to `./`
- **target** - target folder on remote machines to synchronize to
- **include** - rsync include filter
- **exclude** - rsync exclude filter
- **recursive** - recursively synchronize, defaults to `false`
- **delete** - delete target folder contents, defaults to `false`
- **prescript** - list of commands to execute on remote machines before rsync occurs
- **script** - list of commands to execute on remote machines after rsync occurs

### Example
```yaml
kind: pipeline
type: docker
name: default

steps:
- name: build
  image: gendall/drone-node
  commands:
    - npm install

- name: deploy
  image: gendall/drone-rsync
  settings:
    hosts:
      - test-deploy.gendall.io
    key:
      from_secret: ssh_key
    target: /usr/src/app
```

### Important
The script passed to **script** will be executed on remote machines directly after rsync completes to deploy the files. It will be executed step by step until a command returns a non-zero exit-code. If this happens, the entire plugin will exit and fail the build.

## Deployment

This image will be automatically built and deployed to [DockerHub](https://hub.docker.com/u/gendall) when a [Semver](https://semver.org) tag is pushed to the repo.
