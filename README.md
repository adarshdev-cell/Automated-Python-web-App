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
      git clone https://github.com/adarshdev-cell/Automated-Python-web-App.git

      cd Automated-Python-web-App
      ```

  ### Step 2: Install Python and Dependencies

   1. Install Python and pip:
      ```bash
      sudo apt install python3 python3-pip python3-venv -y
      ```

   2. Create a virtual environment and activate it:
      ```bash
      python3 -m venv venv
      source venv/bin/activate
      ```
      
   3. Install the required dependencies:
      ```bash
      pip install -r requirements.txt
      ```

      
   ### Step 3: Create a flask application 

   1. app.py
      ```bash
        from flask import Flask

        app = Flask(__name__)

        @app.route('/')
        def hello():
            return "Hello, this is a Python Flask app."

        if __name__ == "__main__":
          app.run(host='0.0.0.0', port=5000)
      ```

   2. Install Flask
       ```bash
       pip install Flask
       flask --version

       ```
   3. Open a web browser on your local machine and navigate to http://<vm1-public-ip>:5000 to verify the Flask app is running. You should see the application's output.

   4. Stop the Flask app by pressing Ctrl + C in the terminal.


## Setup Jenkins on VM1

  ### Step 1: Install Jenkins for  Ubuntu Linux
   
   1. Install OpenJDK 17 and check the installation
      ```bash
        sudo apt update
        sudo apt install fontconfig openjdk-17-jre
        java -version
        openjdk version "17.0.13" 2024-10-15
        OpenJDK Runtime Environment (build 17.0.13+11-Debian-2)
        OpenJDK 64-Bit Server VM (build 17.0.13+11-Debian-2, mixed mode, sharing)
      ```
   2. Install Jenkins
      ```bash
        sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
        https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
        echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \
        https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
        /etc/apt/sources.list.d/jenkins.list > /dev/null
        sudo apt-get update
        sudo apt-get install jenkins
      ```
   3. Enable Jenkins Service
      ```bash
      sudo systemctl enable jenkins
      ```
   4. Start Jenkins Service
      ```bash
      sudo systemctl start jenkins
      ```
   5. Check Jenkins Status
      ```bash
      sudo systemctl status jenkins
      ```

   6. Unlocking Jenkins

      Browse with http://<vm1-public-ip>:8080 and you will get interface and run this command to see the Administrator Password
       ```bash
        cat /var/jenkins_home/secrets/initialAdminPassword
       ```
      Copy the password and paste it into the Jenkins web interface and install the suggested plugins.

      Create a new Jenkins user and password.

  ### Step 3: Install Jenkins Plugins

   1. In Jenkins, go to Manage Jenkins > Manage Plugins.

   2. Install the following plugins:
      
      a. **GitHub Integration Plugin**: For GitHub webhook integration.

      b. **Pipeline Plugin**: For defining Jenkins pipelines.

      c. **SSH Agent Plugin**: For SSH access to VM2 during deployment.



  ### Configure GitHub Webhook


   1. Go to your GitHub Repository and access the webhook in settings:
      
   2. Add new webhook:
      ```bash
      http://your-jenkins-server-ip:8080/github-webhook/
      ```
   3. Set content type >> Choose application/json.
   
   4. Choose event to trigger >> Just the push event.
    
   5. Save the webhook with add webhook.

  ### Creating Jenkins pipeline for GitHub changes

   1. Go to the Jenkins dashboard and select a create item or job.
    
   2. Enter the item name and select the pipeline and click ok.
     
   4. Item configuration:

      a. Click GitHub hook trigger for GITscm polling.
      b. In pipeline:
      
        ```bash
        pipeline {
             agent any

              environment {
                  VM_USER = 'tiwari'
                  VM_IP = 'your-VM-IP'
              }

              stages {
        
                 stage('Hello') {
                    steps {
                       echo 'Hello World'
                    }
                 }

        
                  stage('Clone Repository') {
                      steps {
                          git branch: 'main', credentialsId: 'Your-github-credential-id', url: 'https://github.com/adarshdev-cell/Automated-Python-web-App.git'
                      }
                  }

        
                   stage('Install Dependencies') {
                      steps {
                          sh '''
                              python3 -m venv venv
                              . venv/bin/activate  # Use '.' instead of 'source'
                              pip install --upgrade pip
                              pip install -r requirements.txt
                          '''
                       }
                    }

                    stage('Build Application') {
                        steps {
                            sh '''
                                . venv/bin/activate
                                # python -m compileall .  # Optional: Precompile Python files
                            '''
                         }
                     }
        
                     stage('Deploy') {
                         steps {
                                 script {
                                     withCredentials([usernamePassword(credentialsId: 'azure-vm-credentials', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                                         sh '''
                                         sshpass -p "$PASSWORD" ssh -o StrictHostKeyChecking=no $USERNAME@your-VM-IP "echo SSH connection successful"
                                         '''
                                      }
                                  }
                          }
                      }
                 }
    
                 post {
                         success {
                             echo 'Deployment successful!'
                         }
                         failure {
                             echo 'Deployment failed. Check logs for errors.'
                         }
                 }
    
         }

        ```

  ### Add credentials 

   Go to Dashboard >> Manage Jenkins >> Credentials:
   
   1. Add credentials for the  second  Azure VM.

   2. Add credentials for the GitHub repository.

   3. Add credentials flask private SSH key.


   **Before updating the flask app.py code**:

   You can see the output on port 5000 with the URL: "your-vm-ip:5000".

   **After updating the app.py**, you can see the different output on port 5000 with the same URL, and you can also check your build pipeline.


   Thanks!

   



      





    

      
      
       

       
       
       

      
      






      

      


    

   
        
    

    

   

    


      





    


 





  


     






      

     
   






