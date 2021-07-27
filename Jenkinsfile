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
            post {
                always {
                    archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                }
            }
        }
        
        //stage('Docker') {
        //    steps {
        //        sh 'echo "*** Docker Stage ***"'
        //        sh 'sudo docker build -t jdk/myboot:1.0 .'
        //    }
        //}
        
        //stage('Deploy to Local') {
        //    steps {
        //        sh 'echo "*** Deploy to local Stage ***"'
        //        sh 'sudo docker run -d --name myboot -p 8180:8180 jdk/myboot:1.0'
        //    }
        //}
        
        stage('SSH transfert') {
            steps {
                script {
                    sshPublisher(publishers: [
                        sshPublisherDesc(configName: 'ec2-host', transfers:[
                            sshTransfer(
                              execCommand: '''
                                    echo "-=- Cleaning project -=-"
                                    sudo docker stop myboot || true
                                    sudo docker rm myboot || true
                                    sudo docker rmi jdk/myboot:1.0 || true
                                '''
                            ),
                            sshTransfer(
                                sourceFiles:"target/*.jar",
                                removePrefix: "target",
                                remoteDirectory: "//home//ec2-user",
                                execCommand: "ls /home/ec2-user"
                            ),
                            sshTransfer(
                                sourceFiles:"Dockerfile",
                                removePrefix: "",
                                remoteDirectory: "//home//ec2-user",
                                execCommand: '''
                                    cd //home//ec2-user;
                                    sudo docker build -t jdk/myboot:1.0 .; 
                                    sudo docker run -d --name myboot -p 8180:8180 jdk/myboot:1.0;
                                '''
                            )
                        ])
                    ])                
                }
            }
        }
    }
}