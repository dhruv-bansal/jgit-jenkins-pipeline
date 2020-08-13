### Jenkins Pipeline for Automatic code versioning
##### (Code that uses [maven](https://maven.apache.org/) as build tool)

## Introduction
This pipeline is abstraction over [gitflow-workflow](https://nvie.com/posts/a-successful-git-branching-model). Basically gitflow is  git branching and release management workflow that helps developers keep track of features, hotfixes and releases in software projects.

## Why Gitflow!
This [blog](https://jeffkreeftmeijer.com/git-flow/) answers everything!
*I like the title of the blog a lot 'why-arent-you-using-git-flow'.

## Implementation of gitflow
This pipeline is backed by with maven plugin [jgit-flow](https://bitbucket.org/atlassian/jgit-flow) which is basically java implementation of gitflow-workflow by atlassian. 
This plugin abstraction out major complexity of gitflow implementation and you can do [semantic versioning](https://semver.org/) by few commands of this plugin e.g. if your current maven code version is 1.0.0-SNAPSHOT *(offcourse that means you are in development).*
Following command will create a release branch that is generally release branch is used for testing and bug fixing purposes and once the code is released, it will also increment the version of the codebase in development.

`mvn jgitflow:release-start -DallowSnapshots -DpushReleases=true -DreleaseVersion=${nextReleaseVersion} -DdevelopmentVersion=${nextDevelopmentVersion}"`

Similarly there are other goals in jgit-flow that helps in implementing complete gitflow-workflow via maven plugin. See [this](https://bitbucket.org/atlassian/jgit-flow/wiki/goals.wiki#!goals-overview) for more details on other goals.

## What this pipeline do
Imagine a scenario where you are following microservices architecture and to maintain your code base you have different git repositories for your different microservices.
Now as per basic microservices principle each microservice will have its independent release cycle that essentially means every microservice can have its own code version. 
Now releasing and maintaining that code version manually via release plugin or even with jgit plugin via commands could be very tedious specially if you have many microservices in your system.

In this scenario this pipeline becomes a handy and fast solution and in single click, it will help you to release your code base for testing *i.e. in gitflow workflow terms start-release* and in another click once testing and bug fixing and done this pipeline will help you to merge the code in master and create a tag *in gitflow terms it means end-release*

And the best part is **single click would orchestate this for all the microservices (git repositories)**. We just have to be follow some conventions and we deciplined in how we commit code and bring it to dev.
Rest everything will be taken care by this pipeline.

## How this pipeline works

##### Step 1: Add jgit maven plugin as code 
Al the time of this pipeline release 1.0-m5.1 version of jgit was latest.
```
 <build>
        <plugins>
            <plugin>
                <groupId>external.atlassian.jgitflow</groupId>
                <artifactId>jgitflow-maven-plugin</artifactId>
                <version>1.0-m5.1</version>
                <configuration>
                    <noDeploy>true</noDeploy>
                    <squash>false</squash>
                    <allowSnapshots>true</allowSnapshots>
                    <autoVersionSubmodules>true</autoVersionSubmodules>
                    <flowInitContext>
                        <masterBranchName>master</masterBranchName>
                        <developBranchName>dev</developBranchName>
                        <releaseBranchPrefix>release-</releaseBranchPrefix>
                        <versionTagPrefix>dt-hgw-</versionTagPrefix>
                    </flowInitContext>
                </configuration>
            </plugin>

           ...
        </plugins>
```
##### Step 2: Add jgit.version file at the root of project
This file will derive the logic of calculating next development version and release version of the codebase when release-start command is executed.

##### Step 3: Configure jenkins pipeline in jenkins using this repository

##### Step 3: Execute that job

##### Step 3a: Start-Release
1. Pipeline takes input all the code repositions links.
1. This pipeline offers two modes right now -
    - start-release
    - end-release
2. Start-Release will execute following steps for all the code repositories
    - If will compare the code of configured 'dev' and 'master' branch and **smartly figure out** if there code changes to release or not
    - If there are changes then pipeline with execute jgitflow:release-start command to release that repo for testing.
    - If no code changes are found then nothing happens for that repository. 

##### Visual workflow for start-release mode
![start-release pipeline flow](http://www.plantuml.com/plantuml/png/VP51Qzj058Jl-ok6z98O6cXxCoM4jXIQKsXxB9YLD99V-lHMxAxizDzNtiYYZd4F0h7pzfiti_Sf-vZ7NfslSuYu-iBT5Nn2mvqR9abg6RnbN3tGVekb8ZwLVxnHwRQvYwGvDrt0sxkZjCOtaonUyH0gN7yQHHli9M_nwAFlcHdJZNFEQvucXp6Z3H6335GnkfcoyDRuaJdWtNeUzgh8MsArgaD3H0uXIGvHc5OhN1_bNkeO4kDEQYQSZTvSyda_-qpyAHOr31ErYjxgBSptXBTcqdgVCkEfT7k9-M6Ddr6wZh6ijaIbDSvf20CUvoFmcVMO9LWfvHX6oaPd8Hm0d7ikiDJxcFKFMh4c5L11DDAsZBIQg9Ztf9s4FGrOFBncI8lFhsvBGXj6QyhC_rUflTQZ-ioNFOpF6RFbiS8TOv9W1_zV5gdqYSiXsXZwVxfes8gnWHYUEictnxbMbBUNz_a-OvSknYJM5GG67JinLovJ4s7XTLyE5ycRpV8y-6Y-vnqamK86LJgfbEL5vroR3VQYYehGq7LygP3MkCEtq0yLhV5LJD8MJw5ozvCyzleN)





