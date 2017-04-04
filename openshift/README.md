Binary Build Scripts
=

This directory houses a number of build scripts that demonstrate the functionality of a binary application build, as well as a promotion pipeline that is managed by Jenkins.

**tsdr-create-developer-project**
-

 -*What it does:*

This script is responsible for creating an OpenShift project for a developer. It takes one parameter - the developer name. 
- It preforms the following tasks:
 - It creates a new OpenShift project, and names it tsdr-dev-[DEV\_NAME]
 - The script supplies the OpenShift project with the data it needs
 - It also instantiates a DB to store the application data

 -*How to use it:*

With the project cloned locally, once inside this directory you can run this script with 

	./tsdr-create-developer-project [DEV_NAME]

The value of [DEV\_NAME] is the name of the developer who will be using this project and has their own forked repository of the source code.

**tsdr-create-pipeline**
-

 -*What it does:*

This script is responsible for creating the required environment for the promotion pipeline to operate in. It will create tsdr-int, tsdr-uat and tsdr-prod projects within the openshift instance, and set up a deployment pipeline in the CICD project to be managed by jenkins.
- It preforms the following tasks:
 - It creates three projects - tsdr-int (for integration), tsdr-uat (for testing) and tsdr-prod (a simulated production environment)
 - It processes a runtime template for each of the projects listed above in order to instantiate them in predictable ways
 - It performs each of the tasks for the developer project script on each of the new projects
 - It establishes a deployment pipeline in the CICD project that is managed by jenkins.

 -*How to use it:*

Sipmly running

	./tsdr-create-pipeline

will perform the necessary steps to establish the promotion pipeline.

**tsdr-delete-pipeline**
-

 -*What it does:*

After running tsdr-create-pipeline your project will be left with a number of projects that you may want to clean away. This script will simply delete all of the project-related data on the OCP instance so you have a clean OCP to work with.

 -*How to use it:*

Simply running

	./tsdr-delete-pipeline

will perform the necessary steps to establish the entire pipeline

**tsdr-init-ocp-mysql-db**
-

 -*What it does:*

This script is responsible for identifying and instantiating the database for the TSDR app. This script will prompt for the username and password for the mysql db, then bring over the relevant payload files for database initialization.
- It performs the following steps:
 - Identifies the MySQL pod that is running on the OCP instance for the tsdr application
 - Rsyncs SQL data files from the local /data/sql folder in the tsdr project to the /var/lib/mysql/data folder on the running MySQL pod
 - Performs an rsh into the pod and sources the copied SQL files as the root user
 - Changes the active MySQL database to tsdr\_mobile - the relevant database for the tsdr application

 -*How to use it:*

After running tsdr-create-developer-project, you can run this script with

	./tsdr-init-ocp-mysql-db [DB_USER] [DB_PASSWORD]

The value of [DB\_USER] and [DB\_PASSWORD] are both provided within the OpenShift web UI after running tsdr-create-developer-project.
