# Flask Application with Automated CI/CD Pipeline on Azure VMs

This repository contains a Flask web application with an automated CI/CD pipeline using Jenkins, deployed on Azure Virtual Machines (VMs). The pipeline is triggered by a GitHub webhook, automating the build, test, and deployment processes. Two Azure VMs are used: one for hosting Jenkins and the Flask application, and the other for users to access the deployed Flask application output via a web browser.


## Prerequisites


 1. An Azure account with an active subscription.
  
 2. A GitHub account and this repository forked or cloned.
  
 3. Two Azure VMs running Ubuntu 24.0 LTS or later.
  
 4. SSH access to both VMs.
  
 5. Python 3.x installed on both VMs.
  
 6. requirements.txt file listing Flask dependencies.
  

## Architecture Overview


   1. **VM1 (Jenkins + Flask Host)**: Hosts Jenkins for the CI/CD pipeline and the Flask application source code. Jenkins automates the build, test, and deployment process.

   2. **VM2 (User Access)**: Host the deployed Flask application, accessible to users via a web browser.

   A GitHub webhook triggers the Jenkins pipeline whenever code is pushed to the repository, automating the deployment process.
   

## Setup Azure VMs


  ### Step 1: Provision Azure VMs
    
   1. Log in to the Azure Portal.
    
   2. Create two Ubuntu 20.04 LTS VMs:

        VM1: Name it Jenkins-Flask-VM.
      
        VM2: Name it Flask-Web-VM.

   3. Configure the VMs:
      
      a. Choose a VM size (e.g., Standard_D2s_v3 for basic testing).

      b. Set up a public IP address for both VMs.

      c. Add inbound ports in the Network Security Group (NSG):
      
         VM1: Port 22 (SSH), Port 8080 (Jenkins), Port 5000 (Flask development).
      
         VM2: Port 22 (SSH), Port 5000 (Flask application).


  ### Step 2: SSH into VM:
   1. Use an SSH client to connect to both VMs:
      ```bash
      ssh -i <path-to-private-key> azureuser@<vm-public-ip>
      ```
   2. Update the VMs:
      ```bash
      sudo apt update && sudo apt upgrade -y
      ```

## Setup Flask Application on VM1

  ### Step 1: Clone the Repository

   1. Install Git:
      ```bash
      sudo apt install git -y
      ```
      
   2. Clone this repository:
      ```bash
      

 





  


     






      

     
   






