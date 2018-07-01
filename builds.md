# Builds

## Get the sources

In order to make development easier, the frontend is included in the backend as a [git submodule](https://git-scm.com/book/en/v2/Git-Tools-Submodules). Then, in order to tinker with the sources, start by getting both of the repos at once with:

``` bash
git clone --recursive https://github.com/filebrowser/filebrowser
```

> NOTE: since it is a submodule, when developing the frontend remember to update the backend repo accordingly.

Alternatively, you can download the repos in two steps with `curl`/`wget`:

``` bash
curl -L https://github.com/filebrowser/filebrowser/archive/master.tar.gz | tar xvz
cd filebrowser-master
curl -L https://github.com/filebrowser/frontend/archive/master.tar.gz | tar xvz
mv frontend-master frontend
```

However, note that this approach won't let you push your changes back easily.

## Build the frontend

In order to build the frontend, just execute the build script with either [yarn](https://yarnpkg.com) or [npm](https://www.npmjs.com/) in the root of the `frontend` repository. It is suggested to clean subdir `dist` first, so that artifacts from previous builds are removed. Also, running `yarn install` to get dependencies up-to-date is encouraged.

``` bash
# Remove existing artifacts (if any)
if [ -d "dist" ]; then
  rm -rf dist/*
fi;
# Get dependencies up-to-date
yarn install
# Build the frontend
yarn build
```

## Run or build the backend

Frontend artifacts are embedded in the backend with [go.rice](github.com/GeertJohan/go.rice/rice). Therefore, prior to following the regular golang procedure this needs to be executed in the root of the `backend` repository:

``` bash
# Install rice tool if not present
if ! [ -x "$(command -v rice)" ]; then
  go get github.com/GeertJohan/go.rice/rice
fi

# Embed the assets using rice
rice embed-go
```

Then, running [dep](https://github.com/golang/dep) to get dependencies up-to-date is strongly recommended:

``` bash
dep ensure -vendor-only
```

Last, the backend can be either run or built from subdir `cmd/filebrowser` as usual:

``` bash
cd cmd/filebrowser
go run
```

or

``` bash
cd cmd/filebrowser
CGO_ENABLED=0 go build -a
cd ../..
cp cmd/filebrowser/filebrowser ./
```

> Note: `CGO_ENABLED=0` is used so that a static binary is built. See [golang.org/cmd/cgo/](https://golang.org/cmd/cgo/).

## Build a Docker image

Since File Browser is compiled to a single static binary with embedded frontend artifacts, just copying it into a [scratch](https://hub.docker.com/_/scratch/) image will work. However, setting some parameters by default is suggested. For example:

```
FROM scratch

VOLUME /tmp
VOLUME /srv
EXPOSE 80

COPY filebrowser /filebrowser
COPY Docker.json /config.json

ENTRYPOINT ["/filebrowser", "--config", "/config.json"]
```

So, put the built binary and a `config.json` in the same dir as the [dockerfile](https://docs.docker.com/engine/reference/builder/) shown above and build it:

``` bash
docker build -t filebrowser/filebrowser .
```

You can then try it:

``` bash
docker run -dp 5555:80 filebrowser/filebrowser --no-auth
```

## Integrate the binary in a external Docker image

There are at least three possible approaches to use File Browser inside a existing image:

- Use a built binary that is locally available, as in the example shown above.
- Get a release tarball and extract it in the dockerfile: 

```
RUN https://github.com/filebrowser/filebrowser/releases/download/v1.8.0/linux-amd64-filebrowser.tar.gz | tar -xvz filebrowser
```
Alternatively, the following snippet will retrieve the latest available tarball:

```
RUN curl -L $( \
         curl -s https://api.github.com/repos/filebrowser/filebrowser/releases/latest \
       | grep "browser_download_url.*linux-amd64.*tar.gz" \
       | cut -d : -f 2,3 \
       | tr -d \" \
     ) \
| tar -xvz filebrowser
```

- Copy the binary from an image that contains it already. E.g. to build a image with a working shell an [Alpine Linux](https://alpinelinux.org/) version can be built from the default scratch-based one:
```
FROM filebrowser/filebrowser AS base
FROM alpine AS img
COPY --from=base /filebrowser <target_path>
```