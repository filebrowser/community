# Contributing

First, get the sources of the backend. Either `git clone https://github.com/filebrowser/filebrowser`, or download with `curl`/`wget`:

``` bash
curl -L https://github.com/filebrowser/filebrowser/archive/master.tar.gz | tar xvz
cd filebrowser-master
```

Then, run either `buil.sh` or `build_in_docker.sh`. In order to run `build.sh` you will need `nodejs`, `yarn`, `go`, and `git`. If you have docker installed, `build_in_docker.sh` will download a ready-to-use image with all the development tools; then `build.sh` will be automatically executed.

Now, build the docker image:

``` bash
docker build -t filebrowser/filebrowser .
```

And try it:

``` bash
docker run -dp 5555:80 filebrowser/filebrowser --no-auth
```
