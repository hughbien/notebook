# Docker for Rails Developers

Build, ship, run your applications everywhere. Book by Rob Isenberg.

Docker provides:

* a holistic view of your app - dependencies like Redis or databases are described and included
  as a fundamental part of your app
* single-command app install and setup - go from zero to running an app in minutes
* easy version management of dependencies
* huge ecosystem - Nginx/Redis/Postgres/Node and so on are already packaged and ready to go
* simulate production locally
* deployment standardization
* reliability and resiliency features - clusters are self-healing
* reducing infrastructure costs - containers are lighter weight than VMs

# Part 1: Development

## Introduction

Search "Docker for Mac" or your system and follow the install instructions. You should be able to
run `docker version` after.

Docker uses **containers** and **images**.

A container is an isolated/sandboxed execution environment. It relies on virtualization features
built into the Linux kernel. Containers can be started/paused/resumed/stopped. Containers are closer
to the metal than VMs.

To start a container, you need to supply it with some specific environment or context. An image
is what makes the container unique. It determines the filesystem, env variables, commands to run,
etc... An image can be spawned into as many containers as you want, that all look the same. An
image is like a factory for creating specific containers. Images are like abstract classes. Containers
are like instances of that class.

To run a Ruby application without Ruby installed:

```
$ docker run ruby:2.6 ruby -e "puts :hello"
```

Docker run uses the format: `docker run [OPTIONS] <image> <command>`. The last command can be split:

* `docker run ruby:2.6` - run a container based on the `ruby:2.6` image
* `ruby -e puts ":hello"` - what to run inside the container

Docker creates a new container on each run. Use `docker ps` to list running containers. Use the
`-a` option to get all containers, including stopped ones. Remove containers with `docker rm <id>`.
You can also use the `--rm` option to remove a container after running the command:

```
$ docker run --rm ruby:2.6 ruby -e "puts :hello"
```

To run multiple commands, you can start a container running an interactive Bash shell.

```
$ docker run -i -t --rm -v ${PWD}:/usr/src/app ruby:2.6 bash
# cd /usr/src/app
# gem install rails
# rails new myapp --skip-test --skip-bundle
# exit
```

Now list the files in your current directory, you'll see a generated Rails app. Using `-v` you can
mount a volume, sharing the local filesystem with the container.

The `-i` option is for input, so that our container can forward input. The `-t` option is to run a
long lived process (bash shell). `-it` is common together to run an interactive process.

## Running a Rails App in a Container

A `Dockerfile` is a blueprint used for creating images. Remember that images are used to create
containers. A Dockerfile is made up of various instructions like: `FROM`, `RUN`, `COPY`, `WORKDIR`.
Here's a basic one for a Rails app:

```
FROM ruby:2.6

RUN apt-get update -yqq
RUN apt-get install -yqq --no-install-recommends
nodejs

COPY . /usr/src/app/

WORKDIR /usr/src/app
RUN bundle install
```

`FROM` starts the image from another one, in this case we're building off the `ruby:2.6` image. The
`RUN` commands tell Docker to execute a command.

The `COPY` command copies all files from our local current directory into `/usr/src/app` on the
filesystem. This copies our Rails application source code. It is always relative to where the
`Dockerfile` is located.

`WORKDIR` sets a working directory. Whenever Docker spins up this container that's produced by
this image, the working directory is where it `cd`s into.

```
$ docker build [options] path/to/build/directory
```

Docker will print out an Image ID. Use `docker images` to get a list of images.

```
$ docker run -p 3000:3000 <our custom image id> bin/rails s -b 0.0.0.0
```

This will now start a Rails server. The `-p 3000:3000` option is used to publish the container's
port 3000 on our port 3000. This means request to `http://localhost:3000` will reach the Rails
server running inside the container. Docker uses a port mapping via `iptables` to forward requests.
Something like `-p 80:3000` would map local 80 to container's 3000.

We use `-b 0.0.0.0` to bind to all IP addresses. Without it, the Rails server would only handle
traffic coming from localhost.

## Fine-Tuning Our Rails Image

Use `docker tag <image id> <name>` to name an image. The tag name can include the image/repository
name and a tag.

```
$ docker tag a1df0eddba18 railsapp
$ docker tag railsapp railsapp:1.0
```

Use the `-t` option to tag an image with the `build` command. After tagging, it's easier to run:

```
$ docker run -p 3000:3000 railsapp:1.0 bin/rails s -b 0.0.0.0
```

There's a `CMD` command, which can be used to execute a default command. So we don't always have to
specify `bin/rails s`:

```
CMD ["bin/rails", "s", "-b", "0.0.0.0"]
```

You can use `CMD bin/rails s -b 0.0.0.0` but it's not recommended because it wraps it in a
`bash -c` which doesn't properly forward Unix signals. `CMD` only specifies a default, the user can
always override what command to run.

To exclude files/directories use a `.dockerignore` file:

```
.git
.gitignore
log/*
tmp/*
*.swp
*.swo
```

Docker builds use a cache, so if your command doesn't change it will just grab from the cache. Cache
busting works for subsequent commands, that is if a command higher up changes then all subsequent
commands also has its cache invalidated. It's recommended to run `apt-get install` commands in
a single line to get around this:

```
RUN apt-get update -yqq && apt-get install -yqq -- no-install-recommends \
  nodejs \
  vim
```

It's useful to copy over the Gemfile and bundle install before anything else, that way a README
change won't trigger having to re-build all gem dependencies:

```
COPY Gemfile* /usr/src/app/
WORKDIR /usr/src/app
RUN bundle install

COPY . /usr/src/app/
```

Docker images can have labels, set via `LABEL key=value`. This is useful for metadata:

```
LABEL maintainer="rob@DockerForRailsDevelopers.com"
```

## Describing Our App Declaratively

Docker Compose manages an application that needs several different containers to work together.
It's declarative. It manages creating/destroying resources needed for the app. Start by creating
a `docker-compose.yml` file:

```yml
version: '3'

services:
  web:
    build: .
    ports:
      - "3000:3000"
```

Compose files always start with a version number. Next you'll have a collection of services. Each
service has configuration options. `build` tells Dockerfile it should use the current directory to
build our image. The `path` is relative to the `docker-compose.yml` file. Ports is used for the
`-p` option.

We need to turn off Ruby's output buffering, since it doesn't play well with Compose. Update
`config/boot.rb` so that `$stdout.sync = true`. Next run:

```
$ docker-compose up
```

This will:

1. create a separate network just for the app
2. create non-locally mounted volumes defined in the app
3. builds an image for any services with a `build` directive
4. creates a container for each service
5. launches a container per service

By default, Compose names the network `<appname>_default`. Images use the default name
`<appname>_<service_name>:latest`. App name is grabbed from the surrounding directory. Compose will
only build images if they don't exist.

Next, Compose will build a container for the image it just created using the default:
`<appname>_<servicename>_<n>`. It will then connect local IO streams.

Use `Ctrl-C` to shut down the container. If it hangs, use `docker-compose stop`. If you don't care
about running container output, use the `-d` option to run in detached mode.

If you run into an issue with Compose, delete the `tmp/pids/server.pid` file and try again.

You can also mount a local volume to share your local filesystem with the container. It lets you
develop locally. We saw this with `-v $PWD:/usr/src/app`. The same thing can be with in Compose:

```yml
  web:
    build: .
    ports:
      - "3000:3000"
    volumes:
      - .:/usr/src/app
```

Containers can have several states: created, running, paused, stopped, or deleted. Use
`docker-compose ps` to check which containers are running. Use `docker-compose stop` or
`docker-compose stop <service>` to stop services. Use `docker-compose start <service>` or
`docker-compose restart <service>` to start/restart.

Use `docker-compose logs -f <service>` to follow the logs of a service.

Use `docker-compose run --rm <service> <cmd>` to run a one-off command that's different than the
default command in the Dockerfile.

Use `exec` instead of `run` to execute commands on a running container.

Use `docker-compose build <service>` to build an image. This is useful if there are multiple
services.

`docker-compose down` stops any running containers and removes them along with the app's dedicated
network/volumes. If you just want to remove the app's container, use `rm`. `docker system prune` is
useful to free up resources.

## Beyond the App: Redis

```
$ docker run --name redis-container redis
```

The `--name` option tells Docker to give your container a human friendly name. Let's modify the
`docker-compose.yml` file to include Redis:

```yml
redis:
  image: redis
```

This service is a lot simpler, it just uses a pre-existing image named `redis`. We don't need to
specify a port or volume. Connect to it with:

```
$ docker-compose run --rm redis redis-cli -h redis
```

This starts the REPL, but how does it connect to our server? Docker Compose creates a new network
for our app. All containers are connected to the app's network and can communicate with each
other. Containers can communicate outside themselves using TCP/IP.

```
$ docker network ls
```

All networks have built in DNS name resolution. So to talk to our web service, we'd use the hostname
`web`. In this case, `redis-cli -h redis` connects to the server using the hostname `redis`.

That means our Rails app can talk to it by connecting via the same hostname.

## Adding a Database: Postgres

Let's add Postgres:

```yml
database:
  image: postgres
  environment:
    POSTGRES_USER: postgres
    POSTGRES_PASSWORD: some-long-secure-password
    POSTGRES_DB: myapp_development
```

The `environment` property lets you set environment variables in your container.

```
$ docker-compose up -d database
```

Connect to it via:

```
$ docker-compose run --rm database psql -U postgres -h database
```

Our Rails application can use the `postgres` hostname to connect to it. We should also configure
our Rails app to use environment variables for sensitive configurations, you can add those into
the `docker-compose.yml` file. We should secure these settings, since we can't commit them.

Create a `.env/development/web` file:

```
DATABASE_HOST=database
```

And create a `.env/development/database` file:

```
POSTGRES_USER=postgres
POSTGRES_PASSWORD=some-long-secure-password
POSTGRES_DB=myapp_development
```

Services can read these files using `env_file`:

```yml
web:
  env_file:
    - .env/development/database
    - .env/development/web
database:
  env_file:
    - .env/development/database
```

But data can't reside in the container, which is ephemeral. It can be decoupled by persisting in
volumes. We've seen a local volume. For this case, it's better to use a named volume: a self-contained
bucket of file storage completely managed by Docker.

```yml
database:
  volumes:
    - db_data:/var/lib/postgresql/data

volumes:
  db_data:
```

Now our data will be persistent across container deletion/creation.

## Testing in a Dockerized Environment

Add RSpec to your Gemfile and use `docker-compose` to rebuild your image. To run tests:

```
$ docker-compose exec web bin/rails generate rspec:install
$ docker-compose exec web bin/rails spec
```

It's simply a matter of running `exec` on an existing container.

## Advanced Gem Management

With the current approach, anytime a change is made to `Gemfile`, all gems are reinstalled from
scratch. This happens because Docker/Bundler are conflicting. Bundler was designed for a long
running system, updating a set of currently installed gems. When `Gemfile` is modified, it busts
the Docker cache.

You can use a Gem Cache Volume:

```
COPY Gemfile* /usr/src/app/
WORKDIR /usr/src/app

ENV BUNDLE_PATH /gems

RUN bundle install
```

Then in `docker-compose.yml`:

```yml
web:
  volumes:
    - .:/usr/src/app
    - gem_cache:/gems
```

Now Gem installations will be cached between builds. If you add a new gem, you can simply run:

```
$ docker-compose exec web bundle install
```

# Part 2: Production

## Introduction

Operations involves:

1. provisioning - creating computing resources
2. configuration management - configuring those resources once they're up
3. release management - deploying new versions of software
4. monitoring and alerting - making sure our services stay up
5. operating - day-to-day tasks, scaling/diagnosing/debugging/updates

There's a new breed of tools to manage multiple containers known as orchestration. They provide a
platform on which we can run and manage containerized applications. Kubernetes handles this.

Infrastructure services include: AWS, Azure, Google Compute, and DigitalOcean.

Chef, Puppet, Ansible are popular tools for configuration management. You'll need them to automate
your host server configuration: eg installing Docker, setting up SSH/users/firewalls. Things you
won't do in a container.

Terraform is a provisioning tool, but it has lightweight capabilities for configuring servers. Some
people can make do with using it to configure their host server before letting Docker containers
do the rest.

## Preparing for Production

Copy the `.env/development` file to `.env/production`. If your Rails app has assets, be sure to
precompile it. Copy over `Dockerfile` to `Dockerfile.prod`, make any changes to it that will differ
from development such as the assets precompile step.

Create a <https://hub.docker.com> account if you don't already have one. This is a Docker Registry,
you can also host your own registry. The URL convention for an image is:

```
[<registry hostname>[:port]/]<username>/<image name>[:<tag>]
```

Build a production image like:

```
$ docker build -f Dockerfile.prod -t username/myapp_web:prod .
```

Next, push it up:

```
$ docker login
$ docker push username/myapp_web:prod
```

Now it's available for other users!
