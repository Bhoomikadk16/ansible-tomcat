pipeline {
    agent any
    tools {
        maven 'maven'
    }
    stages {
        stage('pull the source code from GitHub'){
            steps{
                git branch: 'main', url: 'https://github.com/Bhoomikadk16/ansible-tomcat.git'
            }
        }
        stage('Build with Maven') {
            steps {
                // Invoke top-level Maven to clean and package
                sh 'mvn clean package'
            }
        }
        stage('Send Files Over SSH target') {
            steps {
                sshPublisher(publishers: [
                    sshPublisherDesc(
                        configName: "ssh",
                        transfers: [
                            sshTransfer(
                                sourceFiles: '**/*.war',
                                removePrefix: 'target',
                                execCommand: """
                                """
                            )
                        ]
                    )
                ])
            }
        }
        stage('Send Files Over SSH dockerfile') {
            steps {
                // Send the WAR file and Dockerfile to the remote server
                sshPublisher(publishers: [
                    sshPublisherDesc(
                        configName: "ssh",
                        transfers: [
                            sshTransfer(
                                sourceFiles: 'Dockerfile',
                                removePrefix: '', // No prefix to remove
                                execCommand: """
                                    docker rmi -f tomcat
                                    docker rm -f ansdoc
                                    docker build -t tomcat .
                                    docker run -it -d --name ansdoc -p 8081:8080 tomcat
                                """
                            )
                        ]
                    )
                ])
            }
        }
    }

    post {
        success {
            mail to: 'dkbhoomika2002@gmail.com',
                 subject: "Deployment success!: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                 body: "The build was successful.\n\n Check it out here: ${env.BUILD_URL}"
        }
    }
}