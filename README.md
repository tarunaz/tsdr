TSDR-new
=

File Summaries
-

**Jenkinsfile**

 -*What it is:*

In order for Jenkins to properly manage this application's promotion pipeline, a Jenkinsfile must be included to direct Jenkins and tell it what to do. This file includes each of the automated commands that Jenkins executes on the project throughout the promotion pipeline, and must remain in this directory in order to maintain build stability.

 -*How do you use it:*

The Jenkinsfile won't be manually harnessed at any stage of the build pipeline. This file will be read by the Jenkins instance which will run the commands included. No steps need to be taken to use the Jenkinsfile beyond pointing your Jenkins instance at this gitlab repository for usage.

Directories
-
**logs**

**mysql**

**openshift**

This directory houses the build scripts that demonstrate the functionality of the TSDR app's binary build and promotion pipeline.

**tsdrService**
