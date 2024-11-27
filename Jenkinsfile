pipeline {
    agent any
    
    tools {
        jdk 'jdk'          // Ensure the JDK is configured in Jenkins
        maven 'maven'      // Ensure Maven is configured in Jenkins
    }

    environment {
        SCANNER_HOME = tool 'sonar-scanner'  // Ensure Sonar Scanner is configured in Jenkins
        DOCKER_IMAGE_NAME = 'my-java-app'
    }

    stages {
        stage('Checkout from GitHub') {
            steps {
                git(
                    url: 'https://github.com/Srini-hcl/my-java-app-website.git',  // Replace with your repository URL
                    branch: 'main'  // Replace with your target branch
                )
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean install -DskipTests=true'  // Build the project using Maven
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withCredentials([
                    string(credentialsId: 'SONAR_URL', variable: 'SONAR_URL'),
                    string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TKN')
                ]) {
                    sh '''
                    $SCANNER_HOME/bin/sonar-scanner \
                        -Dsonar.host.url=${SONAR_URL} \
                        -Dsonar.login=${SONAR_TKN} \
                        -Dsonar.projectKey=my-java-app \
                        -Dsonar.java.binaries=.
                    '''
                }
            }
        }

        stage('Docker Build and Push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'mydockergreens') {
                        // Build the Docker image
                        sh 'docker build -t ${DOCKER_IMAGE_NAME}:latest -f docker/Dockerfile .'

                        // Tag the image for Docker Hub
                        sh 'docker tag ${DOCKER_IMAGE_NAME}:latest mydockergreens/${DOCKER_IMAGE_NAME}:latest'

                        // Push the image to Docker Hub
                        sh 'docker push mydockergreens/${DOCKER_IMAGE_NAME}:latest'
                    }
                }
            }
        }

        stage('Transfer Ansible Files') {
            steps {
                sh '''
                scp -i /var/lib/jenkins/.ssh/id_rsa -o StrictHostKeyChecking=no -r ansible/ \
                root@52.90.58.136:/home/ansible/workspace/DevOps-Usecase
                '''
            }
        }

        stage('Deploy via Ansible') {
            steps {
                script {
                    sh '''
                    ssh -i /var/lib/jenkins/.ssh/id_rsa -o StrictHostKeyChecking=no root@52.90.58.136 \
                    "cd /home/ansible/workspace/DevOps-Usecase && \
                    ansible-playbook -i ansible/hosts ansible/deploy.yml"
                    '''
                }
            }
        }
    }
}
