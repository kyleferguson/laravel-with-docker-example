# Laravel Development with Docker

I [wrote a post](https://kyleferg.com/laravel-development-with-docker/) about using Docker for your local Laravel environment.
This repo holds a clean Laravel install setup to use Docker which can be used as a starting point or for reference.

The following is used out of the box with this setup:

- PHP 7 FPM
- Nginx web server
- Mysql database
- Redis for cache

## Run

You'll want to copy the `.env.example` file to `.env` like usual. Run `composer install` to install dependencies and that should be it for the app.

Once you [install Docker](https://docs.docker.com/), you can start the containers using Docker Compose
```sh
$ docker-compose up -d
```

You should be able to visit the app at [http://localhost:8080](http://localhost:8080)
You should also be able to run artisan commands from your local machine :)

To stop the containers you can run `$ docker-compose kill`. If you'd like to remove them all together, after stopping run `$ docker-compose rm`.

## Setup

Here's an overview of the setup. This repo contains the default Laravel 5.2 install other than the files listed below. If you'd like a more in depth explanation of the files below, see the [blog post](https://kyleferg.com/laravel-development-with-docker/) I mentioned above.

`/deploy`
I've added the deploy directory because I like to keep all of the configuration files together (Dockerfiles, service config, etc).
I named it deploy because ideally you would be using the files in here to build images that get deployed (Docker containers). You can obviously
change that as you see fit.

`docker-compose.yml`
This file lets us tell Docker how to build our environment. When we call `docker-compose up` it will read this file and build the necessary containers as well as configure things like networking and volumes.

`deploy/app.docker`
This is the Dockerfile for our app container. It just extends the [PHP base image](https://hub.docker.com/_/php/) provided by Docker and installs some extra extensions that Laravel needs (mcrypt and mysql).

`deploy/web.docker`
This is the Dockerfile for our web container. It extends the [Nginx base image](https://hub.docker.com/_/nginx/) provided by Docker, and just adds an Nginx config file so our web service knows how to handle requests.

`deploy/vhost.conf`
This is the Nginx config file thats added to our web container. It's a pretty standard host configuration that proxy's PHP requests to our app container. You'll notice that it communicates with the app container via address `app:9000`. The `app` name is what we named our service and linked to in `docker-compose.yml`, so Docker will know we mean that container and route the request appropriately.
