# CI/CD pipelining using Git, Github and Jenkins

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

* Now for further changing in this repository let's create one developer branch for keeping the master branch stable by using the following command
      
      git branch branch_name
      e.g.  git branch newdev 
      
* Change to the created branch by using the following command

      git checkout branch_name (in my case it is  newdev)
      
      
  Now we have created a github repository and pushed the files successfully.
