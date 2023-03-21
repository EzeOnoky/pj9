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

- **INSTALL AND CONFIGURE JENKINS SERVER**

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
    
- **Step 2** – Configure Jenkins to retrieve source codes from GitHub using Webhooks
    
- In this part, you will learn how to configure a simple Jenkins job/project (these two terms can be used interchangeably). This job will will be triggered by GitHub [webhooks](https://en.wikipedia.org/wiki/Webhook) and will execute a ‘build’ task to retrieve codes from GitHub and store it locally on Jenkins server.
    
1. - Enable webhooks in your GitHub repository settings
    
    
    
    
