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

      






      

      


    

   
        
    

    

   

    


      





    


 





  


     






      

     
   






