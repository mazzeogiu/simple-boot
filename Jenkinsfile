pipeline {
    agent any
    tools {
        maven 'M3'
    }
    stages {
        stage('Cleaning workspace') {
            steps {
                sh 'echo "*** Cleaning Stage ***"'
                sh 'mvn clean'
                
                script {
                    try {
                    sh 'sudo docker stop myboot && sudo docker rm myboot'
                    } catch (Exception e) {
                        sh 'echo *** No container to remove ***'
                    }
                }
                
                
            }
        }
        stage('Checkout') {
            steps {
                sh 'echo "*** Checkout Stage ***"'
                git branch: 'main', url: 'https://github.com/mazzeogiu/simple-boot.git'
            }
        }
        stage('Compile') {
            steps {
                sh 'echo "*** Compile Stage ***"'
                sh 'mvn compile'
            }
        }
        stage('Test') {
            steps {
                sh 'echo "*** Test Stage ***"'
                sh 'mvn test'
            }
        }
        stage('Package') {
            steps {
                sh 'echo "*** Package Stage ***"'
                sh 'mvn package -DskipTests'
            }
        }
        stage('Docker') {
            steps {
                sh 'echo "*** Docker Stage ***"'
                sh 'sudo docker build -t jdk/myboot:1.0 .'
            }
        }
        stage('Deploy to AWS') {
            steps {
                sh 'echo "*** Deploy to local Stage ***"'
                sh 'sudo docker run -d --name myboot -p 8180:8180 jdk/myboot:1.0'
            }
        }
    }
}