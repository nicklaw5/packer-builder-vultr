# packer-builder-vultr

A Packer builder plugin for creating Vultr snapshots.

## Building with Go

Due to vendoring issues, at the moment this package must be built from within
Packer's source code. So, fetch this repo and Packer:

```bash
go get -d github.com/hashicorp/packer
go get -d github.com/nicklaw5/packer-builder-vultr
```

Copy the contents of `vultr/` to Packer's source tree:

```bash
cp -r ${GOPATH:-~/go}/src/github.com/nicklaw5/packer-builder-vultr/vultr ${GOPATH:-~/go}/src/github.com/hashicorp/packer/builder/
```

Then open up Packer's file `command/plugin.go` and add Vultr as a new builder:

- in `import` section add: `vultrbuilder "github.com/hashicorp/packer/builder/vultr"`
- in `Builders` map add: `"vultr":   new(vultrbuilder.Builder),`

Then you can `go install` Packer, and it will have support for the "vultr" plugin.

## Building with Docker

Before building your Docker image, be sure you've cut a Github release of this repository.
The release version should match the Packer version you are wanting to target.
For example, the below build targets Packer v1.4.2. Which means there should be
a v1.4.2 release of this repository.

Also note that if you're updating to a new version of Packer, be sure to also update
the [`docker/plugin.go`](docker/plugin.go) file. This file is a copy of
https://github.com/hashicorp/packer/blob/master/command/plugin.go, but with
this plugin added.

```bash
docker build \
  --tag packer-builder-vultr \
  --build-arg PACKER_VERSION=v1.4.2 \
  docker
```

You can test your build by running the following:

```bash
cd testdata
docker run --tty --rm \
  --volume ${PWD}:/usr/src/myapp \
  --workdir /usr/src/myapp \
  --env VULTR_API_KEY=your_api_key \
  packer-builder-vultr \
  build vultr.json
```

## Configuration

Check out `testdata/vultr.json` for an example Packer file that uses the Vultr builder.
