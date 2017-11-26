---
layout: post
title:  Continuous Integration and Deploy (part 1 - Atlassian and AWS EBS)
date:   2017-11-23
categories: General AWS Development
math: false
---

# hello-world?

Continuous Integration and Continuous Deploy are key to a more agile development approach and simplifying life / allowing you to focus on building interesting software.  With that in mind I was curious to see how that could be achieved using cloudy tooling and what I could learn from the process.

I'm planning on looking at a number of different options to see what's out in the real world.

I'll use a very simple 'hello, world' endpoint to prove out the deployment piece (just returning some json) and will build using Python3 and the Flask microframework.

<!--more-->

## BitBucket, BitBucket Pipelines and AWS Elastic Beanstalk

The goal here is to leverage [Atlassian BitBucket](https://www.atlassian.com/software/bitbucket) as my git repository and more importantly leverage [Pipelines](https://www.atlassian.com/software/bitbucket/features/pipelines) to achieve the desired CI and CD functionality.  I'm also wanting to ensure that there is some element of code review prior to deployment so have defined a couple of roles, one of the developer (who can write great code) and another individual who can write code but also deploy to a staging environment.

I hadn't looked at [Amazon EBS](https://aws.amazon.com/elasticbeanstalk/) before so I thought it would be interesting to try to leverage this for hosting the software (and it saved messing around with actual machines).

Finally, docker is always entertaining so will throw that into the mix (and gives some structure around what I'm actually running)

### Getting started

#### Pre-requisites

* A local git client
* Python3.4 or greater
* Docker

(While this is being setup as an example for testing BitBucket all the source associated with this project remains on GitHub really to save me managing multiple online Git repositories.  See [here](https://github.com/harbour22/hello-world-svc) for the actual code used here.  There are some good online articles around how to set up a new repository based on an existing one)

Create a new repository on BitBucket.

First of all set up your local venv.  The included gitignore file assumes this is within venv at the root of the project folder.

{% highlight console %}
MacBook-Air:hello-world harbour22$ python3 -m venv venv
{% endhighlight %}

At this point you can source the activate script to setup the local virtual environment

{% highlight console %}
MacBook-Air:hello-world harbour22$ source venv/bin/activate
(venv) MacBook-Air:hello-world harbour22$
(venv) MacBook-Air:hello-world harbour22$ which python
./venv/bin/python
{% endhighlight %}

We also need the AWS CLI and EB software
{% highlight console %}
(venv) MacBook-Air:hello-world harbour22$ pip install awscli --upgrade --user
(venv) MacBook-Air:hello-world harbour22$ pip install awsebcli --upgrade
{% endhighlight %}

finally install the required python dependencies

{% highlight console %}
(venv) MacBook-Air:hello-world harbour22$ pip install -r requirements.txt
{% endhighlight %}

Now, to start the application build the application.  Here we've given it a friendly name helloworld.

{% highlight console %}
(venv) MacBook-Air:hello-world harbour22$ docker build -t helloworld .
## should see lots of output here
(venv) MacBook-Air:hello-world harbour22$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
helloworld          latest              b7fd88073164        22 seconds ago      111MB
{% endhighlight %}

and finally run the application, mapping the external port 4000 to the internal container port 8080

{% highlight console %}
(venv) MacBook-Air:hello-world harbour22$ docker run -p 4001:8080 helloworld
## from a different terminal window
MacBook-Air:hello-world harbour22$ curl -X GET http://localhost:4001/helloworld | python -mjson.tool
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100    24  100    24    0     0   2788      0 --:--:-- --:--:-- --:--:--  3000
{
    "text": "hello, world"
}

{% endhighlight %}

## Entitlements

Within BitBucket I've setup a separate groups to differentiate those who can develop code vs those who can review and merge onto a staging branch

## The Dockerfile (and the Dockerrun.aws.json file)
(updated soon)

## The bitbucket-pipelines.yml file
(updated soon)

## The development lifecycle

This is now fairly straightforward.  Developers can commmit code to the master branch as normal and tests will be run when they are pushed to the remote (BitBucket) Git repository. This allows collaboration across multiple people and the usual development experience you get with Git.

When they are happy that their code is good to go to staging they can submit a pull requests which will be reviewed by someone from the staging group.  

Once the review has happened and they are happy to accept that pull request the code is pulled onto the staging branch and triggers the staging pipeline as defined in the bitbucket-pipelines.yml.  [Deployment is handled through that successful pull request](https://confluence.atlassian.com/bitbucket/deploy-with-pull-requests-856832274.html) This performs a build, test, Dockerfile image creation, uploads the artifact to S3 and then deploys to [AWS EBS](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb-cli3-configuration.html)

Job done!

## (footnote) Initial EB environment setup

I did have to go through an initial setup phase to get my EBS environment setup.  With the command-line tools installed (as part of the pre-requisites) this was fairly strightforward.

{% highlight console %}
(venv) MacBook-Air:hello-world harbour22$ eb init
(venv) MacBook-Air:hello-world harbour22$ eb create
Enter Environment Name
(default is hello-world-dev):
Enter DNS CNAME prefix
(default is hello-world-dev):

Select a load balancer type
1) classic
2) application
3) network
(default is 1): 1
{% endhighlight %}
