#Software Engineering Environments miniproject instruction 


************ Assumptions/Pre-requisites *******************

***Hardware

Laptop with at least 16 Gb memory (recommended 16 Gb, ideally 32 Gb)

*** Software


    Maven (v 3.6.2, or higher)

    Instructions to install here: https://maven.apache.org/download.cgi
    Check installation with the command mvn -version

    VirtualBox(v 6.0, or higher)

    Instructions to install here: https://www.virtualbox.org/wiki/Downloads

    Vagrant (v 2.2.5, or higher)

    Instructions to install here: https://www.vagrantup.com/downloads.html
    (only if using Windows 10 or Windows 8 Pro) Disable Hyper-V, see instructions to disable here: https://www.poweronplatforms.com/enable-disable-hyper-v-windows-10-8/
    Check installation with the command vagrant -v'

    Postman (V 7.19.1 (7.19.1) or higher)

    Instructions to install here: https://www.postman.com/
    
    Git
    
    
    Ansible (v 2.7.5, or higher)

    Instructions to install here: https://docs.ansible.com/
    Check installation with the command ansible --version


# Automate build process 




**** ***************Create integration server*******************

	1- Get to the working directory

	`cd ~/<git_root_folder>/devops/pipeline/integration-server`

	2- Vagrant is used to create a VM which acts as integration server.

	Command : `sudo vagrant up`

	3- Connect to GitLab

	Open `http://192.168.33.9/gitlab` in your browser.

	You will be asked to provide a password (refered as $YOUR_PASSWORD later) for the root credentials.

	To further login with root the credentials are:<br>
	Login: root<br>
	Password: $YOUR_PASSWORD<br>



###NB

#1 The VM is automatically provisioned using Ansible playbooks. 

#2 These playbooks install GitLab, GitLab Runner and Docker . 

#3 GitLab is used as VCS and CI, whereas Docker is used to handle the integration environments.

#4 Upon starting the service up yet another user is requested to be created. Keep the credentials of this new user, as you'll need it for creating repositories.

#5 In case you need to reset the password of a GitLab user follow the instructions given in this [link](https://docs.gitlab.com/ee/security/reset_root_password.html)


# Use GitLab as VCS 

The goal of this step is to make use of GitLab as a VCS. 

1- Create SEEProduct project (in GitLab a project is a repository).<br>
Follow the instructions to create the remote and local repositories.



#NB:

	2- do NOT use the GitLab's root user to create the repo and to push to it<br>

 	use the project placed at:<br>
	`~/<git_root_folder>/devops/SSE_Final_Project`


	2- Commit and push to your created remote repository.


# Automated build

The objective of this stage is to configure the GitLab project such that
every time a developer publish a change on the remote repository, the build process is 
automatically started.


**** [GitLab Runner](https://docs.gitlab.com/runner/) setup


****** Install Git Lab Runner

	1. Ensure you are in the directory<br>
	`~/<git_root_folder>/devops/pipeline/integration-server`

	2. Then ssh to the VM by doing

	Command: `sudo vagrant ssh`


# Register the Runner for Building SSE_Final_Project 

1. First of all, you need to execute the following command:

	`sudo gitlab-runner register`

2. Then enter the requested information as follows:

	For GitLab instance URL enter:<br>
	`http://192.168.33.9/gitlab/`


For the gitlab-ci token enter the generated token.<br>
	`Example: 77NHS6kxWaqmEvkZK22r`

For a description for the runner enter:<br>
	`[integration-server] docker`

For the gitlab-ci tags for this runner enter:<br>
	`buildtags`

For the executor enter:<br>
	`docker`

For the Docker image (eg. ruby:2.1) enter:<br>
	`alpine:latest`


3. Restart the runner:

	`sudo gitlab-runner restart`


Congratualtion Your Automatic Build process is successfully Completed 



#Setup stage environment 

1. The Vagrantfile required to create and run the stage-server VM is provided in this repo. Go to the directory:

```
cd ~/<git_root_folder>/devops/pipeline/stage-server
```

2. Start the staging environment and jump into it.
```
sudo vagrant up
sudo vagrant ssh
```

3. Check Tomcat installation and configuration. 
Open a browser, and try to access to these URLs:
```
http://192.168.33.17:8080
http://192.168.33.17:8080/manager/html
```
**Notes**: 

* User and password are the same: "admin".

* In case these URLs cannot be reached, then try to fix it by restarting tomcat:
```
sudo /opt/tomcat/bin/shutdown.sh
sudo /opt/tomcat/bin/startup.sh
```
# Register the Runner for deploying to stage server 

1. First of all, you need to execute the following command:

	`sudo gitlab-runner register`

2. Then enter the requested information as follows:

	For GitLab instance URL enter:<br>
	`http://192.168.33.9/gitlab/`


For the gitlab-ci token enter the generated token.<br>
	`Example: 77NHS6kxWaqmEvkZK22r`

For a description for the runner enter:<br>
	`[shell`

For the gitlab-ci tags for this runner enter:<br>
	`testserver`

For the executor enter:<br>
	`shell`



7. Restart the runner:
```
	sudo gitlab-runner restart
```



8. Grant sudo permissions to the gitlab-runner
```
	sudo usermod -a -G sudo gitlab-runner
	sudo visudo
```

Now add the following to the bottom of the file:
```
	gitlab-runner ALL=(ALL) NOPASSWD: ALL
```

9.  Restart the staging environment
```
	exit
	sudo vagrant reload
```

**Note**: if the pipeline is not automatically started, then check in the settings of the project if "Auto DevOps" is selected.
To access to these settings, go to "Settings" -> "CI/CD" -> "Auto DevOps".


go to 

```
cd ~/<git_root_folder>/devops/miniproject
```
modify add a new line to end of the modify.txt file  

commit and push file to repository


#Check if the link after completing the pipelining job
```
http://192.168.33.17:8080/SSE_Final_Project/


Congratualtion your stage server is running



# Setup production environment

1. The Vagrantfile required to create and run the production-server VM is provided in this repo. Go to the directory:

```
	cd ~/<git_root_folder>/devops/pipeline/production-server
```

2. Start the staging environment and jump into it.
```
	sudo vagrant up
	sudo vagrant ssh
```

3. Check Tomcat installation and configuration. 
Open a browser, and try to access to these URLs:
```
	http://192.168.33.18:8080
	http://192.168.33.18:8080/manager/html
```
**NB**: 

* User and password are the same: "admin".

* In case these URLs cannot be reached, then try to fix it by restarting tomcat:
```
sudo /opt/tomcat/bin/shutdown.sh
sudo /opt/tomcat/bin/startup.sh
```

# Register the Runner for deploying to stage server

1. First of all, you need to execute the following command:

	`sudo gitlab-runner register`

2. Then enter the requested information as follows:

	For GitLab instance URL enter:<br>
	`http://192.168.33.9/gitlab/`


For the gitlab-ci token enter the generated token.<br>
	`Example: 77NHS6kxWaqmEvkZK22r`

For a description for the runner enter:<br>
	`shell`

For the gitlab-ci tags for this runner enter:<br>
	`productiontags`

For the executor enter:<br>
	`shell`



7. Restart the runner:
```
	sudo gitlab-runner restart
```



8. Grant sudo permissions to the gitlab-runner
```
	sudo usermod -a -G sudo gitlab-runner
	sudo visudo
```

Now add the following to the bottom of the file:
```
	gitlab-runner ALL=(ALL) NOPASSWD: ALL
```

9.  Restart the staging environment
```
	exit
	sudo vagrant reload
```

**Note**: if the pipeline is not automatically started, then check in the settings of the project if "Auto DevOps" is selected.
To access to these settings, go to "Settings" -> "CI/CD" -> "Auto DevOps".


go to 

```
cd ~/<git_root_folder>/devops/SSE_Final_Project
```
modify add a new line to end of the readme.txt file  

commit and push file to repository


#Check if the link after completing the pipelining job
```
	http://192.168.33.18:8080/SSE_Final_Project/

Congratualtion production server is running


   
