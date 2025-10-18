pipeline {
    agent any
    environment {
        SONAR_HOME = tool "Sonar"    // Sonar Scanner tool
        REMOTE_HOST = "ubuntu@54.163.75.83"   // Remote Docker server
        REPO_URL = "https://github.com/Abhi-08-Abhishek/wanderlust-CI-CD-pipeline-practice.git"
        REMOTE_DIR = "wanderlust-CI-CD-pipeline-practice" // Folder on remote server
    }

    stages {

        stage("Clone Code from GitHub") {
            steps {
                git url: "${REPO_URL}", branch: "main"
            }
        }

        stage("SonarQube Quality Analysis") {
            steps {
                withSonarQubeEnv("sonar") {
                    sh '''
                        $SONAR_HOME/bin/sonar-scanner \
                        -Dsonar.projectName=wanderlust \
                        -Dsonar.projectKey=wanderlust
                    '''
                }
            }
        }

        stage("OWASP Dependency Check") {
            steps {
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'Dc'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }

        stage("Sonar Quality Gate Scan") {
            steps {
                timeout(time: 2, unit: "MINUTES") {
                    waitForQualityGate abortPipeline: false
                }
            }
        }

        stage("Trivy File System Scan") {
            steps {
                sh "trivy fs --format table -o trivy-fs-report.html ."
            }
        }

        stage("Deploy on Remote Docker Server") {
            steps {
                sshagent(['remote-server-key']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no $REMOTE_HOST "
                            # Clone repo if folder does not exist
                            if [ ! -d ~/${REMOTE_DIR} ]; then
                                git clone ${REPO_URL} ~/${REMOTE_DIR}
                            fi

                            # Navigate to the project folder
                            cd ~/${REMOTE_DIR}

                            # Pull latest changes
                            git pull origin main

                            # Run Docker Compose
                            docker compose up --build -d
                        "
                    '''
                }
            }
        }

    } // end of stages

    post {
        always {
            archiveArtifacts artifacts: 'trivy-fs-report.html, **/dependency-check-report.xml', allowEmptyArchive: true
        }
    }
}
