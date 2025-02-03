pipeline {
    agent any
    environment {
        MAVEN_HOME = tool 'maven'
        DOCKER_HOME = tool 'docker'
    }
    stages {
        stage('Prepare Environment') {
            steps {
                script {
                    echo 'Initialize all the variables'
                    mavenCMD = "${MAVEN_HOME}/bin/mvn"
                    dockerCMD = "${DOCKER_HOME}/bin/docker"
                    tagName = "3.0"
                }
            }
        }
        stage('Git Code Checkout') {
            steps {
                script {
                    try {
                        echo 'Checkout the code from git repository'
                        git 'https://github.com/shrikant-devops/star-agile-banking-finance.git'
                    } catch (Exception e) {
                        echo "Error: ${e.message}"
                        currentBuild.result = 'FAILURE'
                    }
                }
            }
        }
        stage('Build the Application') {
            steps {
                echo "Cleaning... Compiling... Testing... Packaging..."
                sh "${mavenCMD} clean package"
            }
        }
        stage('Containerize the Application') {
            steps {
                echo 'Creating Docker image'
                sh "${dockerCMD} build -t star-agile-project ."
            }
        }
        stage('Configure and Deploy to the Test Server') {
            steps {
                ansiblePlaybook disableHostKeyChecking: true, installation: 'ansible', inventory: '/etc/ansible/hosts', playbook: 'ansible-playbook.yml', vaultCredentialsId: 'ansible-key', vaultTmpPath: ''
            }
        }
    }
    post {
        failure {
            echo 'Build failed'
        }
        success {
            echo 'Build succeeded'
        }
    }
}
