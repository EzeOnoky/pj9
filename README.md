# PROJECT-9
CONTINOUS INTEGRATION PIPELINE FOR TOOLING WEBSITE


## CONTINOUS INTEGRATION PIPELINE FOR TOOLING WEBSITE

## ***TOOLING WEBSITE DEPLOYMENT AUTOMATION WITH CONTINUOUS INTEGRATION. INTRODUCTION TO JENKINS***

- In previous Project 8 we introduced **horizontal scalability** concept, which allow us to add new Web Servers to our Tooling Website and you have successfully deployed a set up with 2 Web Servers and also a Load Balancer to distribute traffic between them. If it is just two or three servers – it is not a big deal to configure them manually. Imagine that you would need to repeat the same task over and over again adding dozens or even hundreds of servers.

- DevOps is about Agility, and speedy release of software and web solutions. One of the ways to guarantee fast and repeatable deployments is **Automation** of routine tasks.

- In this project we are going to start automating part of our routine tasks with a free and open source automation server – [Jenkins](https://en.wikipedia.org/wiki/Jenkins_(software)). It is one of the mostl popular [CI/CD](https://en.wikipedia.org/wiki/CI/CD) tools, it was created by a former Sun Microsystems developer Kohsuke Kawaguchi and the project originally had a named "Hudson".

- Acording to Circle CI, **Continuous integration (CI)** is a software development strategy that increases the speed of development while ensuring the quality of the code that teams deploy. Developers continually commit code in small increments (at least daily, or even several times a day), which is then automatically built and tested before it is merged with the shared repository.

- In our project we are going to utilize Jenkins CI capabilities to make sure that every change made to the source code in GitHub **https://github.com/yourname/tooling** will be automatically be updated to the Tooling Website.

- ***Side Self Study***

- Read about [Continuous Integration](https://circleci.com/continuous-integration/), [Continuous Delivery](https://circleci.com/continuous-integration/) and [Continuous Deployment](https://circleci.com/continuous-integration/).

- ***Task***

- Enhance the architecture prepared in Project 8 by adding a Jenkins server, configure a job to automatically deploy source codes changes from Git to NFS server.

- Here is how your updated architecture will look like upon competion of this project:

<img width="705" alt="Architecture" src="https://user-images.githubusercontent.com/115954100/226529370-7ac20be8-fafe-4f06-a4a3-778898d2fa53.png">

- ## ***INSTALL AND CONFIGURE JENKINS SERVER***

- **Step 1** – Install Jenkins server

1. - Create an AWS EC2 server based on Ubuntu Server 20.04 LTS and name it "Jenkins"

2. - Install [JDK](https://en.wikipedia.org/wiki/Java_Development_Kit) (since Jenkins is a Java-based application)

```
sudo apt update
sudo apt install default-jdk-headless
```

3. - Install Jenkins

```
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt-get install jenkins
```

- Make sure Jenkins is up and running `sudo systemctl status jenkins`

4. - By default Jenkins server uses TCP port 8080 – open it by creating a new Inbound Rule in your EC2 Security Group.

<img width="525" alt="Inbound rule" src="https://user-images.githubusercontent.com/115954100/226531812-41212d5e-f52c-4653-9634-6ba83aed1b2f.png">

5. - Perform initial Jenkins setup.

- From your browser access **http://<Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>:8080**

- You will be prompted to provide a default admin password

<img width="544" alt="Getting started" src="https://user-images.githubusercontent.com/115954100/226532369-2f64c89d-eb13-4fd9-a94b-2a5e37fd04f9.png">

- Retrieve it from your server:

`sudo cat /var/lib/jenkins/secrets/initialAdminPassword`

- Then you will be asked which plugings to install – choose suggested plugins.

<img width="509" alt="Getting started 2" src="https://user-images.githubusercontent.com/115954100/226532908-77aee06e-f160-4ea5-8d20-6baf9aec41d9.png">

- Once plugins installation is done – create an admin user and you will get your Jenkins server address.

- The installation is completed!

<img width="413" alt="Getting started 3" src="https://user-images.githubusercontent.com/115954100/226535153-867d4005-584e-467c-8602-4ce50c2bacc4.png">

- **Step 2** – Configure Jenkins to retrieve source codes from GitHub using Webhooks
    
- In this part, you will learn how to configure a simple Jenkins job/project (these two terms can be used interchangeably). This job will will be triggered by GitHub [webhooks](https://en.wikipedia.org/wiki/Webhook) and will execute a ‘build’ task to retrieve codes from GitHub and store it locally on Jenkins server.
    
1. - Enable webhooks in your GitHub repository settings [Webhooks](https://darey.io/wp-content/uploads/2021/07/webhook_github.gif)
    
2. - Go to Jenkins web console, click "New Item" and create a "Freestyle project"    

<img width="551" alt="Jenkins web console" src="https://user-images.githubusercontent.com/115954100/226733271-5b580313-31ad-4fd5-a652-7a031935bf29.png">

- To connect your GitHub repository, you will need to provide its URL, you can copy from the repository itself
     
<img width="556" alt="Connecting githup repo" src="https://user-images.githubusercontent.com/115954100/226734606-a79e3071-cddb-4a32-b2a0-6fbc11a43f82.png">
    
- In configuration of your Jenkins freestyle project choose Git repository, provide there the link to your Tooling GitHub repository and credentials (user/password) so Jenkins could access files in the repository.
    
<img width="549" alt="SOURCE CODE" src="https://user-images.githubusercontent.com/115954100/226813834-571b51f6-7af0-448e-bc6c-01f9254c6869.png">

- Save the configuration and let us try to run the build. For now we can only do it manually.
Click "Build Now" button, if you have configured everything correctly, the build will be successfull and you will see it under #1
    
<img width="432" alt="Build check" src="https://user-images.githubusercontent.com/115954100/226814356-ed529255-0803-4fca-ad3b-ee05cdd3098c.png">

- You can open the build and check in "Console Output" if it has run successfully.
    
- If so – congratulations! You have just made your very first Jenkins build!
    
- But this build does not produce anything and it runs only when we trigger it manually. Let us fix it.
    
3. - Click "Configure" your job/project and add these two configurations
    
- Configure triggering the job from GitHub webhook:
       
<img width="432" alt="Build check" src="https://user-images.githubusercontent.com/115954100/226815035-f268d978-6392-481d-a133-10d7cb4f63b9.png">

- Configure "Post-build Actions" to archive all the files – files resulted from a build are called "artifacts". [Post-build actions](https://darey.io/wp-content/uploads/2021/07/archive_artifacts.gif).
    
- Now, go ahead and make some change in any file in your GitHub repository (e.g. **README.MD** file) and push the changes to the master branch.    
    
- You will see that a new build has been launched automatically (by webhook) and you can see its results – artifacts, saved on Jenkins server.
  
<img width="501" alt="Status check for build" src="https://user-images.githubusercontent.com/115954100/226816139-57b9704a-c05f-459c-b564-c09ee87ad01d.png">

- You have now configured an automated Jenkins job that receives files from GitHub by webhook trigger (this method is considered as ‘push’ because the changes are being ‘pushed’ and files transfer is initiated by GitHub). There are also other methods: trigger one job (downstreadm) from another (upstream), poll GitHub periodically and others.
    
- By default, the artifacts are stored on Jenkins server locally `ls /var/lib/jenkins/jobs/tooling_github/builds/<build_number>/archive/`
    
## ***CONFIGURE JENKINS TO COPY FILES TO NFS SERVER VIA SSH***
        
**Step 3** - Configure Jenkins to copy files to NFS server via SSH
    
- Now we have our artifacts saved locally on Jenkins server, the next step is to copy them to our NFS server to ** /mnt/apps** directory.
    
- Jenkins is a highly extendable application and there are 1400+ plugins available. We will need a plugin that is called "[Publish Over SSH](https://plugins.jenkins.io/publish-over-ssh/)".
    
1. - Install "Publish Over SSH" plugin.
    
- On main dashboard select "Manage Jenkins" and choose "Manage Plugins" menu item.
    
- On "Available" tab search for "Publish Over SSH" plugin and install it.
       
<img width="565" alt="Publish over ssh installation" src="https://user-images.githubusercontent.com/115954100/226824519-1d39a38f-cf1d-4cb5-ab5a-db460dc87890.png">

2. - Configure the job/project to copy artifacts over to NFS server.
    
- On main dashboard select "Manage Jenkins" and choose "Configure System" menu item.
    
- Scroll down to Publish over SSH plugin configuration section and configure it to be able to connect to your NFS server:
    
1. - Provide a private key (content of .pem file that you use to connect to NFS server via SSH/Putty)
    
2. - Arbitrary name
    
3. - Hostname – can be **private IP address** of your NFS server
    
4. - Username – **ec2-user** (since NFS server is based on EC2 with RHEL 8)
    
5. - Remote directory – **/mnt/apps** since our Web Servers use it as a mointing point to retrieve files from the NFS server
    
- Test the configuration and make sure the connection returns **Success**. Remember, that TCP port 22 on NFS server must be open to receive SSH connections.    
    
