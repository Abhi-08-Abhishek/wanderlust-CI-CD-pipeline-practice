pipeline{
    agent any // run in agent
    environment{   // .env 
        SONAR_HOME= tool "Sonar"    // same name as per sonar token
    }
    stages{  // stages
        stage("Clone Code from GitHub"){
            steps{
                git url: "https://github.com/Abhi-08-Abhishek/wanderlust-CI-CD-pipeline-practice.git", branch: "main"
            }
        }
        stage("SonarQube Quality Analysis"){
            steps{
                withSonarQubeEnv("sonar"){   // same name as per sonar server
                    sh "$SONAR_HOME/bin/sonar-scanner -Dsonar.projectName=wanderlust -Dsonar.projectKey=wanderlust"
                }
            }
        }
        stage("OWASP Dependency Check"){
            steps{
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'Dc'  // Dc is same as per dependency check
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage("Sonar Quality Gate Scan"){
            steps{
                timeout(time: 2, unit: "MINUTES"){   // wait 2 min then
                    waitForQualityGate abortPipeline: false   // not abort pipeline
                }
            }
        }
        stage("Trivy File System Scan"){
            steps{
                sh "trivy fs --format table -o trivy-fs-report.html ."
            }
        }
        stage("Deploy using Docker compose"){
            steps{
                sh "docker-compose up -d"  // run in background
            }
        }
    }
}
