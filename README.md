### Jenkins Pipeline for Automatic code versioning
##### (Code that uses [maven](https://maven.apache.org/) as build tool)

## Introduction
This pipeline is abstraction over [gitflow-workflow](https://nvie.com/posts/a-successful-git-branching-model). Basically gitflow is  git branching and release management workflow that helps developers keep track of features, hotfixes and releases in software projects.

## Why Gitflow!
This [blog](https://jeffkreeftmeijer.com/git-flow/) answers everything!
*I like the title of the blog a lot 'why-arent-you-using-git-flow'.

## Implementation of gitflow
This pipeline is backed by with maven plugin [jgit-flow](https://bitbucket.org/atlassian/jgit-flow) which is basically java implementation of gitflow-workflow by atlassian. 
This plugin abstraction out major complexity of gitflow implementation and you can do [semantic versioning](https://semver.org/) by few commands of this plugin e.g. if your current maven code version is 1.0.0-SNAPSHOT *offcourse that means you are in development.*
Following command will create a release branch that is generally by convention used for testing and once the code is released, it will also increase the version of the codebase in development.

`mvn jgitflow:release-start -DallowSnapshots -DpushReleases=true -DreleaseVersion=${nextReleaseVersion} -DdevelopmentVersion=${nextDevelopmentVersion}"`

Similarly there are other goals in jgit-flow that helps in implementing complete gitflow-workflow via maven plugin. See [this](https://bitbucket.org/atlassian/jgit-flow/wiki/goals.wiki#!goals-overview) for more details on other goals.









