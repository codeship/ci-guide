## intro

One of the most essential parts of any CI process is running your tests. With Codeship's Jet tool for Docker, we wanted to make building your CI/CD pipeline easy - so we made iterating through, debugging and improving your tests both locally and remotely after you push as straightforward as we could.

## running your tests

Let's go back in to our code example for a minute and add a simple, passing test.

In our case, we're just going to check for an existent environmental variable. This isn't a particularly real-world scenario, but it will help demonstrate exactly what Codeship does with your tests and what you should look for.

So, first we're going to...

*STILL NEED TO DO THIS // Create Env Variable //
STILL NEED TO DO THIS // Encrypt Env Variable //
STILL NEED TO DO THIS // Add Rspec To Gems //
STILL NEED TO DO THIS // Write Rspec Test To Check Env //
STILL NEED TO DO THIS // Add To Steps //
STILL NEED TO DO THIS  // Run Jet Steps //*

## looking at log output

Now, after configuring your tests, let's go back to your terminal and run ```jet steps```

This will run your CI process as defined in **codeship-steps.yml**.

You should see something like this indicating our tests ran and passed:

![Screenshot of local test log output](/img)

## tests per branch

Now I want to take a look at a bit of the flexibility you can implement around running your tests.

Let's go back to our **codeship-steps.yml** file and look at the command where we run our tests.

On this step, let's add a new line:

``tag: master``

This tag tells Codeship to only run this tag on the master branch.

So if we go create a new branch in our terminal, commit the code and then run Jet steps, you should see that the test does not run:

``git checkout -B test_branch
git commit . -"Adding a tag to our test step"
jet steps``

![Screenshot of local test log output without a test](/img)

Now, if we switch back to master and try again, you should see something different:

``git checkout master
git merge test_branch
jet steps``

![Screenshot of local test log output](/img)

As you can see here, running our tests on certain branches is super easy. You can imagine creating branches that run all your tests (before deplyments, for instance), branches that only run front-end tests or tests for certain apis (/api/* for instance)... and a ton of other combinations that will streamline your workflows and keep developers prodcutive.

## after testing, push images and/or deploy!

So, now we have images building, a test that runs and a script that runs if that test passes. The next step is to move from CI to CD: [pushing images and deploying your code.](part3)
