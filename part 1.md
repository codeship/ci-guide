## getting started

We're going to walk you through using Codeship to build, test and deploy your Docker applications. Codeship uses a new tool called Jet to turn your existing Docker apps and workflows in to a seamless CI/CD process.

The first thing you want to do is install Jet on your local machine. For Mac users, you can do this through Brew and Linux users can curl the Jet binary directly. [You can see more detailed instructions here.](https://codeship.com/documentation/docker/installation/)

## testing jet

Once Jet is installed, we'll want to test it out. Type 'jet version' to print the version number on screen. Next, type 'jet help' to bring up the help options. There's a lot Jet can do - from running CI to encrypting your credentials, so take some time to play around with what you see when you run 'jet help'.

![Screenshot of Jet version and/or help output](/img)

## make a simple ruby script

Now that we have Jet installed, we're gonna take a few minutes and build a simple little "app" so that we have something to play with. This isn't a real app, we're just going to write a little Ruby script and a Dockerfile to use as case studies. We'll expand on them later on.

First, create a file called Check.rb. In that file we're just going to print our Postgres and Redis versions. If you're wondering how we're printing versions of tools we haven't set up - we'll get there.

In Check.rb, just write and save the following code:

``require "redis"
require "pg"

def exit_if_not expected, current
  puts "Expected: #{expected}"
  puts "Current: #{current}"
  exit(1) if expected != current
end

puts "Redis"
redis = Redis.new(host: "redis")
puts "REDIS VERSION: #{redis.info["redis_version"]}"

sleep 4
postgres_username = "postgres"
postgres_password = ""
test = PG.connect("postgres", 5432, "", "", "postgres", postgres_username, postgres_password)
puts test.exec("SELECT version();").first["version"]``

## create your dockerfile

Next we're going to create a Dockerfile. Hopefully you're pretty familiar with Dockerfiles, Docker Compose and the rest of the standard Docker workflows.

If you're not, and you want to spend a little bit of time getting up to speed on Docker, we highly recommend using these resources as a jumping off point.

- link1
- link2
- link3

Now, if you're ready to get going, we're going to define a simple Dockerfile. So, create your file and drop this code in:

``# base on latest ruby base image
FROM ruby:2.2.1

# update and install dependencies
RUN apt-get update -qq
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y build-essential libpq-dev nodejs apt-utils

# setup app folders
RUN mkdir /app
WORKDIR /app

# copy over Gemfile and install bundle
ADD Gemfile /app/Gemfile
ADD Gemfile.lock /app/Gemfile.lock
RUN bundle install --jobs 20 --retry 5

Add . /app``

As you can see here, we're pulling the ruby base image, creating some directories, installing some gems and then adding our code. That last bit is important because now when we launch our Docker container, the check.rb script we wrote earlier will be inside it and ready to run.

## define your services / compose

So, now we have a script, we have a Docker container that includes this script... now what?

If you're familiar with Docker, then you probably know Docker Compose. If not, you should [take some time to learn a little bit more about it.](https://docs.docker.com/compose/) Essentially, Compose is how you orchestrate what services you want to build and how you want to connect them.

In our case, we want to orchestrate our app (the Dockerfile we just created), as well as a container for Postgres and a container for Redis. Remember, the script we wrote prints version numbers for Postgres and Redis, so we're going to need those services to be able to run it.

So, with Codeship we use Docker Compose as a jumping off point for your CI/CD process. You'll want to create a **codeship-services.yml** file. This is a simple file that lives in your repo and tells Codeship what infrastructure and services to use - and it looks almost exactly like a typical Docker Compose file.

So, once you've created your **codeship-services.yml** go ahead and add the following code to it:

``demo:
  build:
    image: myapp
    dockerfile_path: Dockerfile
  links:
    - redis
    - postgres
redis:
  image: redis:3.0.5
postgres:
   image: postgres:9.3.6``

The first thing this file does is define our *demo* service. It *builds* the Dockerfile and names it *myapp*. The *links* section tells it what services are required for *demo* to run... this this case both *redis* and *postgres*.

Since we reference *redis* and *postgres*, we need to define them as separate services as well. For each, we provide an image - we could build one using separate Dockerfiles but instead we're going to download existing remos from a Docker registry. This is Dockerhub by default but it can be any registry you specify.

One important thing to know is that any time you build a service, such as *demo*, it will automatically spin up containers for every linked service... so if we build *demo*, we end up with three containers: one for the primary service and one for each service.

## pick your steps to run

## run locally

## change version

## run locally again

## next: adding tests
