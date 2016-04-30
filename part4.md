## what's a volume for?

Let's taking a look at volumes, and let's start with *what is a volume and what is it good for*?

First, it's important to know that Codeship does not reuse containers on different steps, meaning there is no way for a container to grab data or information - or maintain changes - made by a previous container or step in your workflow.

This can create some problems. What if you want to create a service responsible for generating all your production assets, or what if you prepare your database in one service and need it to remain the same in the next step when you run your tests?

That's where volumes come in. A volume is mounted on the host, "underneath" your containers, and you can specify which containers can read from volumes defined by other services. This lets you artifact data, generate assets and pass data between services easily - while keeping your services and steps isolated like you would want for a truly high-integrity CI process.

![Image of a volume/container relationship diagram](/img)

## how to set one up

Let's open our services file and find our main application service and make a quick modification.

```
demo:
  build:
    image: myapp
    dockerfile_path: Dockerfile
  links:
    - redis
    - postgres
  volumes:
    - ./tmp:/app
```

The *volumes* directive takes a parameter that maps a host directory (*./tmp*) to a container directory (*/app*). This means that inside of our container, anything written or read from */app* will actually be taking place on the host in the *./tmp* directory.

## making it work

With our volume mounted, we're next going to need to test if it's working. To make sure it's working, we have to do two things: Write to it and read from it in a later step.

Let's prepare for the second part first, reading from the volume in a step that takes place after the step where we write to it. This will guarantee that the volume is accepting data, persisting that data and readable further down in our CI/CD pipeline.

To read from the volume, we'll need to create a separate service in your **codeship-services.yml** file. Let's do that now.

```
volumes_test:
  build:
    image: volumestest
    dockerfile_path: Dockerfile2
  volumes_from:
    - demo
```

There are a few things that are worth noting here. First, we've given the service a new name, a new image name and a new Dockerfile. We want this service to be totally separate from our other service, after all.

Second, we've added a new directive, *volumes_from* and specified that it will read from the volume in our main *demo* service. This is the essential requirement for connecting volumes between services.

Of course, since our new service needs a new Dockerfile, we'll need to create our **Dockerfile2** really quick.

*STILL NEED TO DO THIS // Create Second Dockerfile //*

Now that our service for reading from the volume exists, let's open up our test spec and write a quick test for that service checking for the presence of an artifact in the volume.

*STILL NEED TO DO THIS // Add Rspec Test For Artifact Existing //*

And we'll need to call this test, along with our new service, in our **codeship-steps.yml** file.

*STILL NEED TO DO THIS // Add service and test to steps file //*

Next up, we have to write something in to our volume in our main *demo* service. Let's add a new file called **artifact.sh** and open that up.

*STILL NEED TO DO THIS // Write file to volume //*

We'll also need to call this script in our CI/CD pipeline so that the artifact is actually written. Let's do that in our **codeship-steps.yml** file.

*STILL NEED TO DO THIS // Add step calling artifact script //*

So now we've got a new service that writes an artifact to a volume, as defined in our **Codeship-steps.yml** file. We also have a new test in our original *demo* service that checks to see if the artifact exists before moving on with our image push and deployment. This, in a nutshell, is how volumes are used in your CI/CD pipeline.

## theoretical uses

The one thing this example probably doesn't cover is why YOU might need to use volumes.

Here are some of the most common real world use-cases for volumes:

* Generating assets used by later services ([See an example repo here.](#))
* Populating a database to be tested later in the pipeline ([See an example repo here.](#))
* Passing credentials between services to keep authentication secure ([See an example repo here.](#))

Of course, every application is different so there are a ton of potential reasons your specific application might need volumes that aren't covered here.

## next: parallel, speed, etc

Next we're going to look at sonme of the more powerful and complex things you can begin to stack together to create really fast, efficient or customized CI/CD pipelines with Codeship. [Taking a look at parallel steps, speed, and more.](part4)
