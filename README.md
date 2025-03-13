# Flask Application with Automated CI/CD Pipeline on Azure VMs

This repository contains a Flask web application with an automated CI/CD pipeline using Jenkins, deployed on Azure Virtual Machines (VMs). The pipeline is triggered by a GitHub webhook, automating the build, test, and deployment processes. Two Azure VMs are used: one for hosting Jenkins and the Flask application, and the other for users to access the deployed Flask application output via a web browser.

## Prerequisites

  An Azure account with an active subscription.
  
  A GitHub account and this repository forked or cloned.
  
  Two Azure VMs running Ubuntu 24.0 LTS or later.
  
  SSH access to both VMs.
  
  Python 3.x installed on both VMs.
  
  requirements.txt file listing Flask dependencies.

## Architecture Overview

  **1** **VM1 (Jenkins + Flask Host)**:   Hosts Jenkins for the CI/CD pipeline and the Flask application source code. Jenkins automates the build, test, and deployment process.

  **2** **VM2 (User Access)**:   Host the deployed Flask application, accessible to users via a web browser.

  A GitHub webhook triggers the Jenkins pipeline whenever code is pushed to the repository, automating the deployment process.






