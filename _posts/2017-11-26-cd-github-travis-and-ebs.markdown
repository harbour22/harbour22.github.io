---
layout: single
title:  Continuous Integration and Deploy (part 2 - GitHub, Travis and AWS EBS)
date:   2017-11-26
categories: General AWS Development
math: false
---

# hello-different-world?

[In the previous post](http://thoughts.harbour22.uk/general/aws/development/cd-bitbucket-pipelines-and-ebs/) I looked at CI/CD using BitBucket and BitBucket Pipelines to deploy to AWS EBS.

This time around I'll take a look at how the same can be achieved using GitHub and Travis CI.  If you're following along please follow the pre-requisites from the prior article.
<!--more-->
## Getting started

This page on the [Travis CI site](https://travis-ci.com/getting_started) seemed like a good place to start.  I already had the [hello-world](https://github.com/harbour22/hello-world-svc) project on GitHut so seemed reasonable to take the site up on the offer to log in with GitHub credentials..

***Wrong turn!***  Once logged in I realised I'd gone to the .com address rather than the .org.  The .com site is for private repositories and I guess organisations looking to leverage this for closed source code.  Off I went to the revoke access for OAuth apps in GitHub and a move across to [Travis-CI.org](https://travis-ci.org).  Much better for what we're looking to do here.

Once logged in Travis-CI and the chap with the moustache had finished his work, I was able to find my hello-world-svc repo..

## Key differences

Travis CI and Pipelines use different configuration files to drive their respective development steps.  Pipelines has its [bitbucket-pipelines.yml](https://github.com/harbour22/hello-world-svc/blob/master/bitbucket-pipelines.yml) file and Travis CI has it's [.travis.yml](https://github.com/harbour22/hello-world-svc/blob/master/.travis.yml)

## Creating the .travis.yml file

We're using the same python service created in the prior post so we need to use the slightly different syntax that Travis CI requires.  Again I'm looking to use python 3.4 and use the same requirements.txt we used last time.  I'll run the same set of tests as my check to ensure that the code is what I want to put forward to staging.
{% highlight yaml %}
language: python
python:
  - "3.4"
install:
  - pip install -r requirements.txt
# command to run tests
script:
  - python -m unittest discover tests/
{% endhighlight %}

Once we have a successful run of the tests we can use the built in support for AWS EBS and deployment

{% highlight yaml %}
deploy:
  provider: elasticbeanstalk
  access_key_id: $AWS_ACCESS_KEY_ID
  secret_access_key:
    secure: $AWS_SECRET_ACCESS_KEY
  region: $AWS_REGION
  app: $APPLICATION_NAME
  env: $APPLICATION_ENV
  bucket_name: $AWS_S3_BUCKET
{% endhighlight %}

I defined environment variables for each of the items above within the travis settings for this repo.  It does appear you can encrypt them locally and provide them to travis which I may look into in a future update.

You'll notice here I've not separated out the difference between building on the master branch vs (in what we did last time) a staging branch.  This can easily be achieved by adding the

{% highlight yaml %}
on:
  branch: staging
{% endhighlight %}

to the travis files.  I've held off setting this up as it's functionally identical to how this was achieved under BitBucket (except using GitHub!)

## Final thoughts on CI/CD

I was surprised at how easy it was to set up a robust development lifecycle using off the shelf tooling and join them all together.  The level of Integration on BitBucket with the rest of the Atlassian tooling is commendable however the ecosystem around GitHub is extensive..
