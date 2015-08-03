## Quick Example for a Go Worker (3 minutes)

This example will show you how to compile your code with the same architecture we have on IronWorker so it will
run properly.

**NOTE**: Be sure you've followed the base [getting started instructions on the top level README](https://github.com/iron-io/dockerworker).

We need to have the
dependencies available in the docker container while building, so let's install the dependencies and vendor them:

```sh
go get github.com/iron-io/iron_go/worker
godeps save -r
```

And build it:

```sh
docker run --rm -v "$GOPATH":/gopath -e "GOPATH=/gopath" -v "$(pwd)":/worker -w /worker iron/images:go-1.4 sh -c 'go build -o hello'
```

Now let's run the program in a container using the iron image:

```sh
docker run --rm -v "$(pwd)":/worker -w /worker iron/images:go-1.4 sh -c './hello -payload hello.payload.json -config hello.config.yml -id 123'
```

Since it works, let's package it up:

```sh
zip -r hello-go.zip .
```

And upload it:

```sh
iron worker upload --name hello-go --zip hello-go.zip iron/images:go-1.4 ./hello
```

Now queue up a task (or 1 million):

```sh
iron worker queue -payload-file hello.payload.json --wait hello-go
```
