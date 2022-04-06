# gopaddle CLI Automation Test Suite
[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

This Test Suite is completely written in bash script & expect script.
### Prerequisites
Below are the tools/dependencies which requires to run the Test Suite locally.

```sh
$ sudo apt-get update -y
$ export DEBIAN_FRONTEND=noninteractive
$ sudo apt-get install expect -y
$ sudo apt-get install wget jq git wdiff curl -y
```
Currently `nodeJs`, `Python`, `Java` and `Go` applications are automated in the Test Suite. So need to install all the above languages on the local machine.

```sh
# NodeJs installation...
$ curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
$ sudo apt-get -y install nodejs
...
# Python installation...
$ sudo apt-get update && sudo apt-get install -y python python-pip
$ sudo apt-get -y install python3-pip
...
# Java installation...
$ sudo apt-get install default-jre -y
$ sudo apt-get update -y
$ sudo apt-get install maven -y
$ mvn -version
$ java -version
...
# go installation...
$ wget https://dl.google.com/go/go1.13.linux-amd64.tar.gz
$ sha256sum go1.13.linux-amd64.tar.gz
$ tar -C /usr/local -xzf go1.13.linux-amd64.tar.gz
$ export PATH=$PATH:/usr/local/go/bin
$ go version
```
Make sure 'gpctl' is installed & Configured on your local machine. Follow the steps listed in [gopaddle help](https://help.gopaddle.io/en/articles/5116592-installing-and-configuring-gopaddle-command-line-utility).
### _Environment variables to be exported_
Change the values according to the gopaddle account & the project which you are using.

```sh
$ export MONGODB_HOST=130.198.22.118:27017
$ export END_POINT=https://portal.gopaddle.io
$ export GP_LOGIN=<gopaddle_login>
$ export GP_PASSWORD=<gopaddle_password>
$ export USE_DEFAULT_PROJECT=NO
[YES/NO]
$ export CREATE_NEW_CLUSTER=YES
[YES/NO]
```
> Note: `MONGODB_HOST=130.198.22.118:27017` is the MongoDB host IP address which is running on the test suite VM.

### _How to get `<release_id>`, `<distribution_id>` & `<project_id>`_
On Left side pannel,
1. Click on Artifacts -> Distributions
2. In the Distribution's list 'view' the default distribution. 
3. On the URL bar you will get the `<release_id>` & `<distribution_id>`. The URL is something like below:-

```sh
http://localhost:3000/release/relaeaf710fe1e54e4678e96e6e55ac5d46f7e/distribution/dis34ae683ae5edde4df5ea147eb80599b7398?releaseName=default
```
In the above url 'relaeaf710fe1e54e4678e96e6e55ac5d46f7e' is release_id & 'dis34ae683ae5edde4df5ea147eb80599b7398' is the distribution_id.
For getting the project ID, on the top title bar, 
1. click on 'settings' icon & click 'Projects' icon.
2. View the 'default' project by clicking on it. The URL is something like below:-
```sh
http://localhost:3000/project/prj9a7e1198e87b7e4ddeea34fe6edfcaff7147
```
In the above url 'prj9a7e1198e87b7e4ddeea34fe6edfcaff7147' is the project_id.
### _How to use Github Access Token_
1. Create an access token by following the steps here https://docs.github.com/en/github/authenticating-to-github/keeping-your-account-and-data-secure/creating-a-personal-access-token
2. Give `all repo` permissions while creating the access token
3. Once access token is generated, you might be asked for the username and password during your git command. Provide the token as the password. But if you are not asked for a password, use the following format for git commands.

```sh
git <command> https://[username]:[accesstoken]@github.com/[organization-name]/[repo.git]
eg. git clone https://[username]:[accesstoken]@github.com/bluemeric/ui.git 
```
### _How to run the `All Test Case` on local machine_
Fork the Test Suite project to your github account. Follow the below steps.
```sh
$ git clone https://[username]:[accesstoken]@github.com/bluemeric/gpctl-testsuite.git
$ cd gpctl-testsuite/
$ chmod +x setUp.sh
$ chmod +x cleanUp.sh
$ bash -x cleanUp.sh
$ bash -x setUp.sh
```

##### `Create Dependency Resource:`
Create all the gpctl dependency resources (Registry, github scm(Need to be register gopaddle-demo handle), cluster & allocation policy):-

```sh
# Project Initial Setup. 
$ cd initialSetup
$ bash -x runAll.sh
[ $USE_DEFAULT_PROJECT = "YES" ] && export PROJECT_ID=`jq .projects[0].id ./templates/output/project_List_Resp.json | tr -d '"'` || export PROJECT_ID=`jq .ProjectID ./templates/output/project_Create_Resp.json | tr -d '"'`
$ export RELEASE_ID=`jq .releases[0].id ./templates/output/release_List_Resp.json | tr -d '"'`
$ export DISTRIBUTION_ID=`jq .distribution[0].id ./templates/output/distribution_List_Resp.json | tr -d '"'`
[ $CREATE_NEW_CLUSTER = "YES" ] && export CLUSTER_ID=`jq .id ./templates/output/cluster_Create_Resp.json | tr -d '"'` || echo "GPCTL INIT will take pre-existing cluster"
$ cd ..
```
> Note: If you are using pre-existing project, you can skipe the `Create Dependency Resource` part. Instead you have to export the below envs:

```sh
$ export PROJECT_ID=<project_id>
$ export RELEASE_ID=<release_id>
$ export DISTRIBUTION_ID=<distribution_id>
```
```sh
$ bash -x runAll.sh
```
The above steps will initiate the regression on your local machine. It will take `1 hrs & 30 mins` to finish.
### _How to run the `Specific Test Case` on local machine_
Fork the Test Suite project to your github account. Follow the below steps.

```sh
$ git clone https://[username]:[accesstoken]@github.com/bluemeric/gpctl-testsuite.git
$ cd gpctl-testsuite/
$ chmod +x setUp.sh
$ chmod +x cleanUp.sh
$ bash -x cleanUp.sh
$ bash -x setUp.sh
```

##### `Create Dependency Resource:`
Create all the gpctl dependency resources (Registry, github scm(Need to be register gopaddle-demo handle), cluster & allocation policy):-

```sh
# Project Initial Setup. 
$ cd initialSetup
$ bash -x runAll.sh
[ $USE_DEFAULT_PROJECT = "YES" ] && export PROJECT_ID=`jq .projects[0].id ./templates/output/project_List_Resp.json | tr -d '"'` || export PROJECT_ID=`jq .ProjectID ./templates/output/project_Create_Resp.json | tr -d '"'`
$ export RELEASE_ID=`jq .releases[0].id ./templates/output/release_List_Resp.json | tr -d '"'`
$ export DISTRIBUTION_ID=`jq .distribution[0].id ./templates/output/distribution_List_Resp.json | tr -d '"'`
[ $CREATE_NEW_CLUSTER = "YES" ] && export CLUSTER_ID=`jq .id ./templates/output/cluster_Create_Resp.json | tr -d '"'` || echo "GPCTL INIT will take pre-existing cluster"
$ cd ..
```
> Note: If you are using pre-existing project, you can skipe the `Create Dependency Resource` part. Instead you have to export the below envs:
```sh
$ export PROJECT_ID=<project_id>
$ export RELEASE_ID=<release_id>
$ export DISTRIBUTION_ID=<distribution_id>
```
```sh
# Specify the application folder name which you want to Test locally.
$ cd gpctl-testsuite/apps/<App_Folder>
$ bash -x runAll.sh
```
### _How to get Logs_
`Type-1:` To checking the whole Test-Suite's logs:
```sh
$ cd gpctl-testsuite/
$ tail -f logs/logs.out
```
`Type-2:` To check a specific application Test-Case logs:
```sh
$ cd gpctl-testsuite/apps/
# Specify the application folder name which you want to check.
$ cd <Folder_Name>
$ tail -f logs/logs.out
```
Similary you can look for `help` & `Import` logs.
> Note: The whole TestSuite logs `(Type-1)` will not contain much informations. The specific Test-Case logs `(Type-2)` only contains the complete informations about the failure.
### _How to check Test Reports:-_
Go to the project root folder & follow the steps:
```sh
$ cd gpctl-testsuite/
$ echo "GPCTL Testsuite Report - "`pwd`"/results/results.html"
```
Copy & paste the report file path on the chrome browser.
### _How to add new applications (Test-Cases) on Regression_
1. Find the application (or) take the apps from init application list(Excel sheet).
2. Run the application on locally & make sure that the app is working fine.
3. Upload all the script files on the app's github account.
4. Use the below command to create new test case folder for this application
```sh
$ cd gpctl-testsuite/apps/
$ cp -R mern-todo-list <New-Application-Folder-Name>
```
5. Modify the below mentioned values on the newly created Test-Case folder.
```sh
$ cd gpctl-testsuite/apps/
$ cd <New-Application-Folder-Name>
# Change the "cloneUrl" value according to the project.
$ vi input/input.json
# Re-Name the expect scrips file name with this app name
$ cd scripts
$ mv expect-mern-todo-list-init.sh expect-<New-Application-Folder-Name>-init.sh
$ mv validate-mern-todo-list-init.sh validate-<New-Application-Folder-Name>-init.sh
```
5. Open the Testsuite in VS Code.
6. Expand the newly created app folder & go to the scripts folder.
7. View the expect init script & modify the options according to the new test cases.
8. Similarly view all the validation script files & rename the Scenarios.
9. View the 'runAll.sh' script file inside the newly created project folder & update the test-case name[Application Name].
10. View the cleanUp.sh script on the Testsuite root folder & add the below command at the end of the init test section.
```sh
echo "Deleting <New-Application-Folder-Name> init test logs";
rm apps/<New-Application-Folder-Name>/output/actual/*.out
echo {} > apps/<New-Application-Folder-Name>/results/results.json
rm -rf apps/<New-Application-Folder-Name>/code/<New-Application-Folder-Name>
```
Replace <New-Application-Folder-Name> with actual app name.

11. View the runAll.sh script on the Testsuite root folder & add the below command at the end of the init test section.
```sh
cd ../<New-Application-Folder-Name>
chmod +x runAll.sh
bash -x runAll.sh
echo "Executing the cleanup scripts - <New-Application-Folder-Name>"
bash -x scripts/init-cleanUp.sh
bash -x scripts/validate-cleanUp.sh
```
Replace <New-Application-Folder-Name> with actual app name.

12. View the setUp.sh script on the Testsuite root folder & add the below command at the end of the init test section.
```sh
chmod +x apps/<New-Application-Folder-Name>/scripts/*.sh
```
Replace <New-Application-Folder-Name> with actual app name.
### _Procedures for CheckIn the Testsuite Code_
1. Before the `code commit` must execute the cleanup scripts.
```sh
$ cd gpctl-testsuite/
$ chmod +x cleanUp.sh
$ bash -x cleanUp.sh
$ cp results/temp_report.html results/results.html
```
2. Below are the commands for CheckIn the code.
```sh
$ git status
$ git add .
$ git commit -m "<Commit Description>"
$ git push origin main
```
> Note: `Commit Description` must be a meaningful sentence i.e, exactly specify the changes which you did on the Testsuite also attach the `Jira ID`. 

> Best Practice: Before Pushing the local code to the github need to `fetch` the latest code from an organization & `merge` it with your code changes.
### _How fetch the code from `Organization`_
For fetching the latest code from organization need to change the below config changes on your `.git/config` file.
```sh
$ cd gpctl-testsuite/
$ vi .git/config
# Add the mentioned "upstream" block below to the "[remote "origin"]" block & save the file.
[remote "upstream"]
        url = https://github.com/bluemeric/gpctl-testsuite.git
        fetch = +refs/heads/*:refs/remotes/upstream/*
```
```sh
$ git fetch upstream
$ git merge upstream/main
```
> Note: Before merging the organization code must `commit` your local changes. Otherwise your local changes will be override.

### _Running the GPCTL Regression on Jenkins_
Below params you have to create on jenkins.
```sh
$ export MONGODB_HOST=130.198.22.118:27017
$ export END_POINT=https://portal.gopaddle.io
$ export GP_LOGIN=<gopaddle_login>
$ export GP_PASSWORD=<gopaddle_password>
$ export USE_DEFAULT_PROJECT=NO
[YES/NO]
$ export CREATE_NEW_CLUSTER=YES
[YES/NO]
```
For running the gpctl regression on jenkins will not need to specify the project Id, release Id, Distribution id as a param. After initializing the project setup shell script itself we are handling thoses envs. The below script will run the initial setup & export the required envs. After it will run the gpctl test-cases.

```sh
$ export MONGODB_HOST=$MONGODB_HOST
$ export END_POINT=$END_POINT
$ export GP_LOGIN=$GP_LOGIN
$ export GP_PASSWORD=$GP_PASSWORD
$ export USE_DEFAULT_PROJECT=$USE_DEFAULT_PROJECT
$ export CREATE_NEW_CLUSTER=$CREATE_NEW_CLUSTER
$ gpctl --version   
$ sum `which gpctl`
$ chmod +x setUp.sh
$ chmod +x cleanUp.sh
$ bash -x cleanUp.sh
$ bash -x setUp.sh
# Project Initial Setup...
$ cd initialSetup
$ bash -x runAll.sh

[ $USE_DEFAULT_PROJECT = "YES" ] && export PROJECT_ID=`jq .projects[0].id ./templates/output/project_List_Resp.json | tr -d '"'` || export PROJECT_ID=`jq .ProjectID ./templates/output/project_Create_Resp.json | tr -d '"'`

$ export RELEASE_ID=`jq .releases[0].id ./templates/output/release_List_Resp.json | tr -d '"'`
$ export DISTRIBUTION_ID=`jq .distribution[0].id ./templates/output/distribution_List_Resp.json | tr -d '"'`

[ $CREATE_NEW_CLUSTER = "YES" ] && export CLUSTER_ID=`jq .id ./templates/output/cluster_Create_Resp.json | tr -d '"'` || echo "GPCTL INIT will take pre-existing cluster"

$ cd ..
# Running the whole regression
$ bash -x runAll.sh
```

### Tree Structure of the Project

```sh
$ tree gpctl-testsuite/
gpctl-testsuite/
├── apps
│   ├── django-student-management-system
│   │   ├── code
│   │   │   └── Info.md
│   │   ├── input
│   │   │   └── input.json
│   │   ├── logs
│   │   │   └── logs.out
│   │   ├── output
│   │   │   ├── actual
│   │   │   │   └── info.md
│   │   │   └── expected
│   │   │       └── setup.out
│   │   ├── results
│   │   │   ├── results.html
│   │   │   ├── results.json
│   │   │   └── temp-result.json
│   │   ├── runAll.sh
│   │   └── scripts
│   │       ├── clone.sh
│   │       ├── expect-django-student-management-system-init.sh
│   │       ├── expect-setup.sh
│   │       ├── init-cleanUp.sh
│   │       ├── setup.sh
│   │       ├── validate-cleanUp.sh
│   │       ├── validate-django-student-management-system-init.sh
│   │       └── validate-setup.sh
│   ├── gopone
│   │   ├── code
│   │   │   └── Info.md
│   │   ├── input
│   │   │   └── input.json
│   │   ├── logs
│   │   │   └── logs.out
│   │   ├── output
│   │   │   ├── actual
│   │   │   │   └── info.md
│   │   │   └── expected
│   │   │       └── setup.out
│   │   ├── results
│   │   │   ├── results.json
│   │   │   └── temp-result.json
│   │   ├── runAll.sh
│   │   └── scripts
│   │       ├── cleanUp-old.sh
│   │       ├── clone.sh
│   │       ├── expect-gopone-init.sh
│   │       ├── expect-setup.sh
│   │       ├── init-cleanUp.sh
│   │       ├── setup.sh
│   │       ├── validate-cleanUp.sh
│   │       ├── validate-gopone-init.sh
│   │       └── validate-setup.sh
│   ├── Hostel-Management
│   │   ├── code
│   │   │   └── Info.md
│   │   ├── input
│   │   │   └── input.json
│   │   ├── logs
│   │   │   └── logs.out
│   │   ├── output
│   │   │   ├── actual
│   │   │   │   └── info.md
│   │   │   └── expected
│   │   │       └── setup.out
│   │   ├── results
│   │   │   ├── results.json
│   │   │   └── temp-result.json
│   │   ├── runAll.sh
│   │   └── scripts
│   │       ├── clone.sh
│   │       ├── expect-Hostel-Management-init.sh
│   │       ├── expect-setup.sh
│   │       ├── init-cleanUp.sh
│   │       ├── setup.sh
│   │       ├── validate-cleanUp.sh
│   │       ├── validate-Hostel-Management-init.sh
│   │       └── validate-setup.sh
│   ├── mern-todo-list
│   │   ├── code
│   │   │   └── Info.md
│   │   ├── input
│   │   │   └── input.json
│   │   ├── logs
│   │   │   └── logs.out
│   │   ├── output
│   │   │   ├── actual
│   │   │   │   └── info.md
│   │   │   └── expected
│   │   │       └── setup.out
│   │   ├── results
│   │   │   ├── results.html
│   │   │   ├── results.json
│   │   │   └── temp-result.json
│   │   ├── runAll.sh
│   │   └── scripts
│   │       ├── cleanUp-old.sh
│   │       ├── clone.sh
│   │       ├── expect-mern-todo-list-init.sh
│   │       ├── expect-setup.sh
│   │       ├── init-cleanUp.sh
│   │       ├── setup.sh
│   │       ├── validate-cleanUp.sh
│   │       ├── validate-mern-todo-list-init.sh
│   │       └── validate-setup.sh
│   ├── Multiport-1
│   │   ├── code
│   │   │   └── Info.md
│   │   ├── input
│   │   │   └── input.json
│   │   ├── logs
│   │   │   └── logs.out
│   │   ├── output
│   │   │   ├── actual
│   │   │   │   └── info.md
│   │   │   └── expected
│   │   │       └── setup.out
│   │   ├── results
│   │   │   ├── results.json
│   │   │   └── temp-result.json
│   │   ├── runAll.sh
│   │   └── scripts
│   │       ├── cleanUp-old.sh
│   │       ├── clone.sh
│   │       ├── expect-multiport-init.sh
│   │       ├── expect-setup.sh
│   │       ├── init-cleanUp.sh
│   │       ├── setup.sh
│   │       ├── validate-cleanUp.sh
│   │       ├── validate-multiport-init.sh
│   │       └── validate-setup.sh
│   ├── Multiport-2
│   │   ├── code
│   │   │   └── Info.md
│   │   ├── input
│   │   │   └── input.json
│   │   ├── logs
│   │   │   └── logs.out
│   │   ├── output
│   │   │   ├── actual
│   │   │   │   └── info.md
│   │   │   └── expected
│   │   │       └── setup.out
│   │   ├── results
│   │   │   ├── results.json
│   │   │   └── temp-result.json
│   │   ├── runAll.sh
│   │   └── scripts
│   │       ├── cleanUp-old.sh
│   │       ├── clone.sh
│   │       ├── expect-multiport-init.sh
│   │       ├── expect-setup.sh
│   │       ├── init-cleanUp.sh
│   │       ├── setup.sh
│   │       ├── validate-cleanUp.sh
│   │       ├── validate-multiport-init.sh
│   │       └── validate-setup.sh
│   ├── python-helloworld
│   │   ├── code
│   │   │   └── Info.md
│   │   ├── input
│   │   │   └── input.json
│   │   ├── logs
│   │   │   └── logs.out
│   │   ├── output
│   │   │   ├── actual
│   │   │   │   └── info.md
│   │   │   └── expected
│   │   │       └── setup.out
│   │   ├── results
│   │   │   ├── results.json
│   │   │   └── temp-result.json
│   │   ├── runAll.sh
│   │   └── scripts
│   │       ├── cleanUp-old.sh
│   │       ├── clone.sh
│   │       ├── expect-python-helloworld.sh
│   │       ├── expect-setup.sh
│   │       ├── init-cleanUp.sh
│   │       ├── setup.sh
│   │       ├── validate-cleanUp.sh
│   │       ├── validate-python-helloworld.sh
│   │       └── validate-setup.sh
│   ├── realtime-voting-reactjs
│   │   ├── code
│   │   │   └── Info.md
│   │   ├── input
│   │   │   └── input.json
│   │   ├── logs
│   │   │   └── logs.out
│   │   ├── output
│   │   │   ├── actual
│   │   │   │   └── info.md
│   │   │   └── expected
│   │   │       └── setup.out
│   │   ├── results
│   │   │   ├── results.json
│   │   │   └── temp-result.json
│   │   ├── runAll.sh
│   │   └── scripts
│   │       ├── clone.sh
│   │       ├── expect-realtime-voting-reactjs-init.sh
│   │       ├── expect-setup.sh
│   │       ├── init-cleanUp.sh
│   │       ├── setup.sh
│   │       ├── validate-cleanUp.sh
│   │       ├── validate-realtime-voting-reactjs-init.sh
│   │       └── validate-setup.sh
│   └── Shopping-Cart
│       ├── code
│       │   └── Info.md
│       ├── input
│       │   └── input.json
│       ├── logs
│       │   └── logs.out
│       ├── output
│       │   ├── actual
│       │   │   └── info.md
│       │   └── expected
│       │       └── setup.out
│       ├── results
│       │   ├── results.json
│       │   └── temp-result.json
│       ├── runAll.sh
│       └── scripts
│           ├── cleanUp-old.sh
│           ├── clone.sh
│           ├── expect-setup.sh
│           ├── expect-shopping-cart-init.sh
│           ├── init-cleanUp.sh
│           ├── setup.sh
│           ├── validate-cleanUp.sh
│           ├── validate-setup.sh
│           └── validate-shopping-cart-init.sh
├── cleanUp.sh
├── help
│   ├── logs
│   │   └── logs.out
│   ├── output
│   │   ├── actual
│   │   │   └── info.md
│   │   └── expected
│   │       ├── help-dependency.out
│   │       ├── help-general.out
│   │       ├── help-import.out
│   │       ├── help-init.out
│   │       ├── help-login.out
│   │       └── setup.out
│   ├── README.md
│   ├── results
│   │   ├── results.json
│   │   └── temp-result.json
│   ├── runAll.sh
│   └── scripts
│       ├── expect-dependency.sh
│       ├── expect-help-general.sh
│       ├── expect-help-import.sh
│       ├── expect-help-init.sh
│       ├── expect-help-login.sh
│       ├── expect-setup.sh
│       ├── setup.sh
│       ├── validate-dependency.sh
│       ├── validate-help-general.sh
│       ├── validate-help-import.sh
│       ├── validate-help-init.sh
│       ├── validate-help-login.sh
│       └── validate-setup.sh
├── import
│   └── wordpress-mysql
│       ├── code
│       │   └── Info.md
│       ├── input
│       │   └── input.json
│       ├── logs
│       │   └── logs.out
│       ├── output
│       │   ├── actual
│       │   │   └── info.md
│       │   └── expected
│       │       └── setup.out
│       ├── results
│       │   ├── results.html
│       │   ├── results.json
│       │   └── temp-result.json
│       ├── runAll.sh
│       └── scripts
│           ├── cleanUp-old.sh
│           ├── clone.sh
│           ├── expect-setup.sh
│           ├── expect-wordpress-mysql-import.sh
│           ├── import-cleanUp.sh
│           ├── setup.sh
│           ├── validate-cleanUp.sh
│           ├── validate-setup.sh
│           └── validate-wordpress-mysql-import.sh
├── login
│   └── README.md
├── logout
│   └── README.md
├── logs
│   └── logs.out
├── README.md
├── results
│   ├── results.html
│   ├── results.json
│   ├── sample.html
│   ├── temp_report.html
│   └── temp-result.json
├── runAll.sh
└── setUp.sh

103 directories, 207 files
```
