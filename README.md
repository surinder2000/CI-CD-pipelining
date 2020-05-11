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
* In the Build Triggers section select Poll SCM, put * * * * * in Schedule box for checking the github in every minute
* In the Build Environment section click on Add build step, from the dropdown menu select Execute shell and then put the following code in the Command box
          
          sudo cp * /root/myweb/

          if sudo docker ps -a | grep testos 
          then
            sudo docker rm -f testos
          fi

          sudo docker run --rm -dit -v /root/myweb:/usr/local/apache2/htdocs/  --name testos httpd
          
  It will copy the webpages into the myweb folder which is already created in the server system (in my case it is RHEL 8). I am using docker for testing and deployment of the site. Here if condition will check whether in the docker testos container is already running or not, if it is already running then it will remove it. A new docker container is launched for testing the website before deploy it which contains the same environment that is used in deployment container for deploying the webisite.
  
* Click on Apply and Save button with this Test job will be created successfully

#### 2b. Second job for Deployment


#### 3b. Third job for triggering the Deploy job by the QAT (Quality Assurance Team)



