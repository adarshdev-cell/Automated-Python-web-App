pipeline {
    agent any

    environment {
        REPO_URL = 'https://github.com/adarshdev-cell/Automated-Python-web-App.git'
        APP_DIR = '/home/adarsh/my-app'
        SERVER_USER = 'JenkinsVM'
        SERVER_IP = '13.71.21.221'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: "${REPO_URL}"
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                cd ${WORKSPACE}
                python3 -m venv venv
                source venv/bin/activate
                pip install -r requirements.txt
                '''
            }
        }

        stage('Deploy to Server') {
            steps {
                sshPublisher(
                    publishers: [
                        sshPublisherDesc(
                            configName: "flask-server",
                            transfers: [
                                sshTransfer(
                                    sourceFiles: '**',
                                    removePrefix: '',
                                    remoteDirectory: '${APP_DIR}',
                                    execCommand: '''
                                    cd ${APP_DIR}
                                    source venv/bin/activate
                                    pip install -r requirements.txt
                                    systemctl restart flaskapp
                                    '''
                                )
                            ]
                        )
                    ]
                )
            }
        }
    }
}
