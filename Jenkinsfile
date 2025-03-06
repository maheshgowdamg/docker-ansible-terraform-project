pipeline {
    agent any
    tools {
        maven 'maven'
    }
    stages {
        stage("Pull SRC") {
            steps {
                git branch: 'main', url: 'https://github.com/maheshgowdamg/docker-ansible-terrafrom.git'
            }
        }
        stage("move the target") {
            steps {
                sh 'mv target/webapp.war .'
            }
        }
        stage("Prepare Build") {
            steps {
                sh 'mvn clean package'
            }
        }
        stage("build docker image"){
            steps{
                sh 'docker build -t app .'
            }
        }
        stage("tag image"){
            steps{
                sh 'docker tag app maheshgowdamg25/webapp:latest'
            }
        }
        stage("push image"){
            steps{
               sh 'echo "Mahi@2001"| docker login -u maheshgowdamg25 --password-stdin'
                sh 'docker push maheshgowdamg25/webapp:latest'
            }
        }
        stage("remove images locally"){
            steps{
                sh 'docker rmi  app'
            }
        }
        
        stage("run ansible playbook") {
            steps {
                sshPublisher(
                    continueOnError: false, 
                    failOnError: true,
                    publishers: [
                        sshPublisherDesc(
                            configName: "remote",
                            transfers: [
                                sshTransfer(sourceFiles: 'play.yml'),
                                sshTransfer(execCommand: "ansible-playbook play.yml")
                            ],
                            verbose: true
                        )
                    ]
                )
            }
        }
    }
}
