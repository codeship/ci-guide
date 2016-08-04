## now we release code or images (or both)

Now that you have a working CI process with a successful test, the next step is to push out your Docker image!

# required services before we push

## a Codeship account with access to Jet

The first thing you'll need is a working Codeship account with access to Jet. If you haven't used up a 14 day free trial yet, you can [sign up for one here.](https://www.codeship.com/docker)

If you have an account already - or, once you've activated your free trial - you'll want to create a new project. When creating your new project, make sure to select the Docker infrastructure after choosing the repo you're using.

![Selecting Docker Infrastructure](/images/setup_select_docker.png)

If you don't have a repo configured, just create a new repo on Github and push the code examples we're using so far.

## an image repo account

Now, you're also gonna need an image repo account. For this example, we're gonna go with Dockerhub but you could use Quay.io or your own self-hosted image repo.

## let's do a push

Now that we have our accounts created, let's go ahead and open up our **codeship-steps.yml** file. After our test step, we're going to add a new step - a *push* step.

Add the following code, and then we'll go through it, to discuss what's happening and what we need to do to make it work.

```
- type: parallel
  steps:
    - name: checkrb
      service: demo
      command: bundle exec ruby check.rb
    - name: test
      service: demo
      command: bundle exec ruby test.rb

- type: serial
  steps:
    - name: dockerhub_push
      service: demo
      type: push
      image_name: account/repo
      registry: https://index.docker.io/v1/
      encrypted_dockercfg_path: dockercfg.encrypted
```

There are a few things to note here:

*Push* is the step type we use to signify that we're pushing out the image defined or built by our service.

*Image name* takes a slightly different form depending on the repo - if it's Dockerhub, it's *account_name/repo_name*. This is your repo account name and then the name of the specific repo on your account you're pushing to. You'll need to review specific documentation if you're using Quay.io / AWS ECR or a private repo to make sure the name is defined correctly.

*Registry* is the unique push URL for the image repo. Again, this varies per registry so if you're not using Dockerhub be sure to verify that you get the right value for this.

*Encrypted_dockercfg_path* is where we grab the credentials for your image repo account from. But, why is it encrypted and how did we encrypt it? Let's take a look at that now...

## encrypted credentials

So, just like we did with our environmental variabels in the previous lesson, we'll want to encrypt our image repository examples so that our repo doesn't have our logins sitting around in plain text.

First, if you didn't do it on the last step, you'll need to download yur project's AES key in to your code's directory. To get the AES key, just go to your project on Codeship.com and look at the project settings.

![Downloading AES key](/images/aes_key.png)

Now that you've downloaded your AES key, we'll created a new file called dockercfg where we'll drop in our Dockerhub credentials:

```
{
	"auths": {
		"https://index.docker.io/v1/": {
			"auth": "auth_key",
			"email": "email"
		}
	}
}
```

Once we've saved that, we'll switch over to our terminal and ```cd``` in to the correct directory.

From there, we'll run:

```
jet encrypt dockercfg dockercfg.encrypted
```

You should see your new dockercfg.encrypted file populate. Now, you'll want to remove your unencrypted version - or, at the very least, add it to your **.gitignore** file.

## push!

Now, assuming you've swapped in your credentials in the push step and created your encrypted Dockercfg file, it's time to push all this up and run our build!

Let's go back to our terminal and run:

```
git add .
git commit . -m "Deploying our image"
git push origin master
```

## look at web ui and repo

Now that we've got our build running, let's hop over the web UI and look for evidence of our successful push.

When we click in to our build logs, we should able to click our push step and see something like this:

![Push step log output](/images/pushlogs.png)

Now, if we go over to our Dockerhub repo, we should see some meta data indicating a successful push:

![Dockerhub repo screenshot](/images/dockerhub-recent-push.png)

## up next: using volumes

Now we've built an app, added a test, pushed an image and deployed our code. What's next?

We want to walk through a couple of the powerful, flexible things you can achieve with Codeship, Jet and Docker - and we're going to start with *volumes*. [Let's learn about volumes.](part4.md)
