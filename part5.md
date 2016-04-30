## intro

Now that we've covered the basic pieces of the service, we should take a few minutes and cover two additional features that make for more flexible and powerful CI/CD workflows for your team.

## parallelizing

The first optimization, and one of the most common and most immediately useful, is to parallelize the steps defined in your **Codeship-steps.yml** file.

Let's open up that file and modify our existing commands.

*STILL NEED TO DO THIS // Parallelize both tests //*

So using this basic syntax, we've defined that we're going to run *parallel* type steps, then listed our two steps. Now, rather than running serially, these two steps will run on their own containers at the same time - letting us get through them much faster.

In the web UI, parallel steps are grouped together but maintain separate log output.

![Parallel steps web UI](/img)

## cacheing

Next up, let's take a look at cacheing. When you use cacheing, we'll do an invisible push to your repo with a special tag letting us know that this image is for us to reuse. Then, on your next build, we'll quickly check your Dockerfile to see if anything has changed. If nothing has changed - meaning, if building the Dockerfile would result in the exact same image as last time - we'll reuse the cached image. This is a layer by layer cache, so we'll reuse as much of the image as we can before rebuilding the rest of the image once we encounter a change.

Let's open up our **Codeship-services.yml** file and make a change to enable cacheing.

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
  cached: true
```

Now, let's push our build and take a look at the log output.

![Log output around pushing cache.](img)

To see our cache in action, we'll have to push another build using the same image up, so let's comment out part of our **codeship-steps.yml** file just for this example and push up a new build.

*STILL NEED TO DO THIS // Comment out step //*

Once the new build runs, we can check our log output and see our cache in action.

![Cacheing working log output.](img)

Cacheing is a really powerful way to speed your builds up. We also have a great article on optimizing your builds overall, as well as making sure your Dockerfile is designed with caching in mind. [You can read that here.](buildspeedguide)

## learn more!

From here, there's still a ton more you can learn to optimize your builds, troubleshoot your problems and build more complex and productive workflows.

We recommend [our blog](blog), [our documentation](documentation), [our community forum](communityforum), and [our webinars](webinars) to keep learning more.

It's important to get to a working build as soon as possible when you start your new CI/CD process with Codeship and Docker. From there, take some time with your team every few weeks or every few months to find ways to optimize, save time, keep the developers coding with fewer waiting periods and improve your application and Docker image efficiency.
