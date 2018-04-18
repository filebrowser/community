# Contributing

First, get the sources of the backend:

``` bash
curl -L https://github.com/filebrowser/filebrowser/archive/master.tar.gz | tar xvz
cd filebrowser-master

Then, run either `buil.sh` or `build_in_docker.sh`.

Now, build the docker image:

``` bash
cd filebrowser
docker build -t filebrowser/filebrowser .
```

And try it:

``` bash
docker run -dp 5555:80 filebrowser/filebrowser --no-auth
```
