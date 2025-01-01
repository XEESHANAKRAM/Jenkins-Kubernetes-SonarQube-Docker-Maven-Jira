pipeline {
    agent any

    tools {
        maven 'maven3'
        jdk 'jdk17'
    }

    environment {
        SONAR_HOME = tool 'sonar-scanner'
        DOCKER_IMAGE = 'xeeshanakram/demo:latest'
        KUBECONFIG = '/root/.kube/config' // If you're using a custom path for kubeconfig
    }
    
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/XEESHANAKRAM/Jenkins-Kubernetes-SonarQube-Docker-Maven-Jira.git'
            }
        }

        stage('Code Compile') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh ''' 
                    $SONAR_HOME/bin/sonar-scanner \
                    -Dsonar.projectKey=demo \
                    -Dsonar.projectName=demo \
                    -Dsonar.java.binaries=target/classes
                    '''
                }
            }
        }

        stage('Code Build') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Docker Build Image') {
            steps {
                sh 'docker build -t demo:latest .'
            }
        }

        stage('Docker Push Image') {
            steps {
                withDockerRegistry([credentialsId: 'docker', url: 'https://index.docker.io/v1/']) {
                    sh 'docker tag demo:latest xeeshanakram/demo:latest'
                    sh 'docker push xeeshanakram/demo:latest'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Deploy to Kubernetes using kubectl
                    sh '''
                        kubectl apply -f k8s/deployment.yaml
                    '''
                }
            }
        }
    }
}
