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

`require "redis"
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
puts test.exec("SELECT version();").first["version"]`

## create your dockerfile

Next we're going to create a Dockerfile. Hopefully you're pretty familiar with Dockerfiles, Docker Compose and the rest of the standard Docker workflows.

If you're not, and you want to spend a little bit of time getting up to speed on Docker, we highly recommend using these resources as a jumping off point.

- link1
- link2
- link3

## define your services / compose

## pick your steps to run

## run locally

## change version

## run locally again

## next: adding tests
