pipeline {
    agent any
    environment{
        Sonar_Home= tool "Sonar"
    }
    stages{
        stage("Code from GitHub"){
            steps{
                git url: "https://github.com/rks007/wanderlust.git", branch: "main"
            }
        }
        stage("SonarQube quality Analysis"){
            steps{
                withSonarQubeEnv("Sonar"){
                    sh "$SONAR_HOME/bin/sonar-scanner -Dsonar.projectName=wanderlust -Dsonar.projectKey=wanderlust"
                }
            }
        }
        stage("OWASP dependency check"){
            steps{
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'dc'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage("Sonar Quality Gate Scan"){
            steps{
                timeout(time: 2, unit: "MINUTES"){
                    waitForQualityGate abortPipeline: false
                }
            }
        }
        stage("Trivy File System Scan"){
            steps{
                echo "trivy fs --format table -o trivy-fs-report.html ."
            }
        }
        stage("Deploy"){
            steps{
                sh "docker compose up -d"
            }
        }
    }
}
