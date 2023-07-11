# CI/CD pipeline

**CI/CD, which stands for Continuous Integration and Continuous Deployment (or Continuous Delivery), is a set of practices and automated processes used in software development to ensure efficient and reliable delivery of applications.**

## What value does does it add ?

---

By implementing a CI/CD pipeline, development teams can achieve faster release cycles, reduce the risk of introducing bugs or regressions, and ensure a higher level of quality in their software deployments. It adds business value as it saves a lot of time and resources. 

## Why Jenkins is good for CI/CD?

---

* Ease of use
* Large community support
* Open-source
* Plugin Ecosystem

## Other tools for CI/CD pipeline

---

* **GitLab CI/CD**: GitLab CI/CD is a built-in continuous integration and continuous deployment solution provided by GitLab, a web-based Git repository management tool. It offers an integrated CI/CD pipeline configuration within the same platform, making it a seamless experience.
  
* **CircleCI**: CircleCI is a cloud-based CI/CD platform that focuses on simplicity and speed. It provides a highly customizable and easy-to-configure pipeline setup.

* **Azure DevOps**: Azure DevOps, provided by Microsoft, is a comprehensive suite of development tools that includes CI/CD capabilities.

* **AWS CodePipeline**: AWS CodePipeline is a fully managed CI/CD service provided by Amazon Web Services. It allows you to define and orchestrate your build, test, and deployment pipelines using a visual interface or YAML code.

## Business Value

---

* Faster time-to-market. It help automates complicated tasks within minutes jobs. Faster and efortless software development process

* Less resources and management.

* Improved Collaboration and Transparency: CI/CD promotes collaboration between development, testing, and operations teams

## CI/CD Pipeline with Jenkins and AWS

---

![diagram](./images/pipeline.png)


* Jenkins supports webhooks as a means of triggering CI/CD pipelines and jobs based on external events or notifications. Webhooks allow Jenkins to receive HTTP callbacks from external systems or services, notifying it of specific events or changes. When a webhook event occurs, Jenkins can be configured to automatically initiate a pipeline build or trigger a specific job. (Analogy to ordering a food online and receiving updates what's happening with your order).

* We need to generate another pair of SSH keys for jenkins and connect it with github to allow Jenkins cloning repo automatically form github. Public key to github repo and private key to Jenkins.

* **Master Node**: The master node is like the boss of Jenkins. It manages the overall Jenkins system and controls everything. It decides what needs to be done, when it should be done, and who should do it. It stores the instructions and configuration for your pipelines and handles the user interface that you interact with.

* **Agent Node**: Agent nodes are like the workers in Jenkins. They are the machines that actually do the work. When the master node tells them to do something, they perform the assigned tasks. Agent nodes can be separate computers, virtual machines, or containers. They provide the computing power and resources needed to run your builds, tests, and deployments.

In simpler terms, the master node is the brain of Jenkins, managing and organizing everything, while the agent nodes are the muscles, doing the actual work according to the master's instructions. In our case it will be testing the app before deployment.


The master node tells the agent nodes what to do, and they carry out the tasks. This division of work allows Jenkins to handle multiple jobs and distribute the workload across different machines, making the whole process faster and more efficient.

Master Node is separated from Agent Node because in Master all github, production and the rest of things is included. If testing breaks it is separated from Master and helps us to reduce the load from master node. It is also runs on AWS.

* We will need to provide the pem file for our AWS to Jenkins so it has rights to deploy the app on our behalf. Also on our E2C instance we will need to add security rule for Jenkins.

## Creating SSH key pair and adding public key to GitHub

`ssh-keygen -t rsa -b 4096 -C "kf.dudek@gmail.com"` - command to generate ssh key pair

**Add SSH Key to GitHub:**

* Log in to your GitHub account.
  
* Go to "app" repo and then to settings by clicking on your profile picture.
  
* In the left sidebar, click on "Deploy keys"
Click on "New SSH key."

* Give the key a title (e.g., "Jenkins SSH key").
  
* Copy the contents of the public key file and paste it into the "Key" field.
* Click on "Add SSH key" to save it.


![diagram](./images/ssh-key-github.png)


# Setting up integration between Jenkins and Github

![](./images/diagram_webhook.png)

* Step 1: Coppy HTTPS url from github repo and add it to the project url field

![](./images/1.png)

![](./images/HTTPS-git.png)

* Step 2: In Source Code Management select 'GIT' and copy SSH url from github repo.
  
![](./images/2.png)

![](./images/SSH-git.png)

* Step 3: Add credentials by copying private key (the pair of the public key from the app repo with the same name and exact content)

![](./images/3.png)

* Step 4: Change the branch name for main

![](./images/4.png)

* Step 5: In build Environment select provide Node & npm bin/folder to PATH andd choose SParta-Node-JS so it can test our app 

![](./images/node_env.png)

* Step 6: In Office 365 Connector we need to restrict where node agent will test the app to separate it from the Node master

![](./images/agent.png)

* Step 7: testing commands

![](./images/test.png)

* Step 8: Save the job and you are ready to build now to test.

# How to create webhook on GitHub with Jenkins

**Configuring GitHub**

* Step 1: go to your GitHub repository and click on ‘Settings’.

* Step 2: Click on Webhooks and then click on ‘Add webhook’.

* Step 3: In the ‘Payload URL’ field, paste your Jenkins environment URL. At the end of this URL add /github-webhook/. In the ‘Content type’ select: ‘application/json’ and leave the ‘Secret’ field empty.

![](./images/webhook1.png)

* Step 4: In the page ‘Which events would you like to trigger this webhook?’ choose ‘Let me select individual events.’ Then, check ‘Pull Requests’ and ‘Pushes’. At the end of this option, make sure that the ‘Active’ option is checked and click on ‘Add webhook’.


![](./images/webhook2.png)
![](./images/webhook3.png)

**Configuring Jenkins**

* Step 5: In Jenkins, click on ‘New Item’ to create a new project.

* Step 6: Click on the ‘Build Triggers’ tab and then on the ‘GitHub hook trigger for GITScm polling’. Or, choose the trigger of your choice.

![](./images/webhook4.png)

**After all of those steps, Jenkins will run the job everytime we will push or pull changes from the app repo**

