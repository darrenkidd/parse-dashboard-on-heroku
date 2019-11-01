## Building Locally with Docker

I'm currently running everything via [Docker](https://www.docker.com/) with no tools intalled on my local machine (except Docker... :whale:).

This is just a reminder for me on how to install/upgrade dependencies within this workflow.

### Build Custom Image

We need `git` and `openssh` in order to install some of our packges. The [Node-Alpine](https://hub.docker.com/_/node/) images don't come with this pre-installed, so I'll just add another layer to add these packages.

Using the cool `STDIN` technique (no `Dockerfile` required!).

```bash
$ docker build -t npm-with-git -<<EOF
FROM node:12.12.0-alpine
RUN apk add --no-cache bash git openssh
EOF
```

* I could probably use the `current-alpine` image because I don't think I'll get any version clashes (and I don't lock down engine versions either). But I'm using this for now.

### Run Commands

```bash
$ MSYS_NO_PATHCONV=1 docker run -it --rm -v "$(pwd)":/app --workdir /app npm-with-git npm outdated
...
$ MSYS_NO_PATHCONV=1 docker run -it --rm -v "$(pwd)":/app --workdir /app -v /app/node_modules npm-with-git npm install
...
```

* The `MSYS_NO_PATHCONV=1` environment variable is present because I develop on Windows and Git Bash gets confused when trying to auto-convert `/` to Windows paths. Blergh.
* Using a volume "trick" to force the `node_modules` folder to live in the container so that dependencies are built properly.
