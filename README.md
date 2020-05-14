# Automating testing and deployment operation using Git, Github, Docker and Jenkins

In this project i am going to integrate Git, Github, Docker and Jenkins to automate testing and deployment opeartion of software/application.

## Prerequisites for this project
* RHEL OS for using it as server
* Must have installed Docker and Jenkins inside RHEL
* Must have installed Git Bash inside windows os
* Must have Github account

## Let's see step by step process for integrating these tools:
### 1. Create one local folder for keeping code files like web pages or other stuff
* Make this folder as repository by using the following command
              
      git init
      
* Add the files of the repository in the staging area of the git by using the following command

      git add .  //This command add all the files in one go
      git add file_name  //This command add the specified file
      
* Commit the files to create a version by using the following command

      git commit . -m 'message'  //This command commit all the files in one go
      git commit file_name -m 'message'  //This command commit the specified file
      
* Now for pushing these files to github, first we need to create a github repository in github and after that we need to link that repository with our local repository by using the following command
 
       git remote add origin repository_url
       e.g. git remote add origin https://github.com/surinder2000/web.git
       
 * After linking remote repository we push the files into remote repository by using the following command
 
       git push -u origin master 
 
   After this for pushing the files into remote repository we will use only
       
       git push

* Now for making changes in the files in this repository let's create one developer branch in order to keep the master branch stable by using the following command
      
      git branch branch_name
      e.g. git branch newdev 
      
* To switch to the other created branch use the following command

      git checkout branch_name (in my case it is  newdev)
      
  
### 2. Let's create Jenkins jobs for automation
* Login to jenkins

#### 2a. First job for testing
* Click on New item in the jenkins dashboard a new window will open
* Enter the job name in the box below _Enter an item name_ (lets say Test job)
* Click on Freestyle project and then press OK then a new window will open for configuring the job
* In Source Control Management section check Git
* Enter the Repository URL and specify the name of the developer branch in Branch Specifier box (in my case it is newdev)

![Git configure](https://github.com/surinder2000/automating_testing_and_deployment_operation/blob/master/test1.jpg)

* In the Build Triggers section select Poll SCM (you can choose any trigger of your own choice i am using Poll SCM), put * * * * * in Schedule box for checking the github in every minute

![Build Trigger configure](https://github.com/surinder2000/automating_testing_and_deployment_operation/blob/master/test2.jpg)

* In the Build Environment section click on Add build step, from the dropdown menu select Execute shell and then put the following code in the Command box
          
          sudo cp * /root/myweb/

          if sudo docker ps -a | grep testos 
          then
            sudo docker rm -f testos
          fi

          sudo docker run --rm -dit -v /root/myweb:/usr/local/apache2/htdocs/  --name testos httpd
          
  It will copy the webpages into the myweb directory which is already created in the server system (in my case it is RHEL 8). I am using docker for testing and deployment of the site. Here if condition will check whether in the docker testos container is already running or not, if it is already running then it will remove it. A new docker container is launched for testing the website before deploy it which contains the same environment that is used in deployment container for deploying the webisite.
  
![Build Environment configure](https://github.com/surinder2000/automating_testing_and_deployment_operation/blob/master/test3.jpg)
  
* Click on Apply and Save button with this Test job will be created successfully

#### 2b. Second job for Deployment
* Again click on New item in the jenkins dashboard a new window will open
* Enter the job name in the box below _Enter an item name_ (lets say Deploy job)
* Click on Freestyle project and then press OK then a new window will open for configuring the job
* In Source Control Management section check Git
* Enter the Repository URL and specify the name of the branch as **master** in Branch Specifier box

![Git configure](https://github.com/surinder2000/automating_testing_and_deployment_operation/blob/master/deploy1.jpg)

* In the Build Triggers section select Poll SCM (you can choose any trigger of your own choice i am using Poll SCM), put * * * * * in Schedule box for checking the github in every minute

![Build Trigger configure](https://github.com/surinder2000/automating_testing_and_deployment_operation/blob/master/deploy2.jpg)

* In the Build Environment section click on Add build step, from the dropdown menu select Execute shell and then put the following code in the Command box

      sudo cp * /root/webdeploy/

      if sudo docker ps | grep deployos
      then
        echo "Already running" 
      else
        sudo docker run --rm -dit -v /root/webdeploy:/usr/local/apache2/htdocs/ -p 8090:80  --name deployos httpd
      fi
      
  It will copy the webpages into the webdeploy directory which is already created in the server system (in my case it is RHEL 8). As I already told you that I am using docker for testing and deployment of the site. Here if condition will check whether in the docker deployos container is already running or not, if it is already running then it is ok but if it is not running then it will launch deployos container and expose it to 8090 port.
  
![Build Environment configure](https://github.com/surinder2000/automating_testing_and_deployment_operation/blob/master/deploy3.jpg)
  
* Click on Apply and Save button with this Deploy job will be created successfully

#### 3b. Third job for triggering the Deploy job by the QAT (Quality Assurance Team)
* Again the first three steps same click on New item in the jenkins dashboard a new window will open
* Enter the job name in the box below _Enter an item name_ (lets say QAT job)
* Click on Freestyle project and then press OK then a new window will open for configuring the job
* In Source Control Management section check Git
* Enter the Repository URL and specify the name of the developer branch in Branch Specifier box (in my case it is newdev)
* In this job we need to provide credentials because we have to merge the developer branch into master
* To provide credentials click on Add, then click on jenkins a new page will show, in that page just put your user name and password of github and leave the other boxes as it is and click on Add. After this click on down arrow of credentials box and select your username and password
* In this section below click on Add button of Additional Behaviours, a drop down menu will pop up, from the menu click on **merge before build**, put the Name of repository as **origin** (if you have other name put that one), Branch to merge to as **master**, leave the other boxes as it is

![Git configure](https://github.com/surinder2000/automating_testing_and_deployment_operation/blob/master/qat1.jpg)

* In the Build Triggers section select Trigger builds remotely (you can choose any trigger of your own choice), put Authentication Token in the box, then the following url is used for triggering the job
 
      JENKINS_URL/job/QAT%20Job/build?token=TOKEN_NAME
      
  Just replace JENKINS_URL with the url of the jenkins and TOKEN_NAME with the token which is set in Authentication Token box
  
![Build Trigger configure](https://github.com/surinder2000/automating_testing_and_deployment_operation/blob/master/qat2.jpg)

* At last go to Post-build Actions section, click on Add post-build action, from the drop down menu select Git Publisher, check Push Only If Build Succeeds and Merge Results, click on Add Branch, put Branch to push as **master**, Target remote name as **origin** (you can put whatever in your case) that's it click on apply and save button and we are done.

![Post-build Actions configure](https://github.com/surinder2000/automating_testing_and_deployment_operation/blob/master/qat3.jpg)

### 3. Let's create one hook for pushing the file into github on commiting
* Go to hooks directory from your git repository by using the follwing command

      cd .git/hooks
      
* Create one file name post-commit (it is a fixed name) using notepad and put the following code inside the file and save it without any extension

      #!/bin/bash
      git push

   This script will push the files to github as soon as we commit the files in git, we don't need to write `git push` command separately
 
 
### 4. Let's test the automation
* First go to developer branch from git repository using following command 

      git checkout branch_name (in my case it is newdev)
      
* Make some changes in one of the webpage
* Commit it that's it
* Now the file will automatically be pushed into github newdev branch due to this first job will be triggered and the code will deploy on testing environment, after this QAT team will check the website and if it is ok then QAT team will trigger the third job by using the url that we have created for triggering the third job which will merge the developer branch code into master branch, and as soon as the code is merged second job will be triggered and it deploy the code on deployment environment. If website will not work properly QAT team will connect to developer in some way and ask for making corrections in the code. 
