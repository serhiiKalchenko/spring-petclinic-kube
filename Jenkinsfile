pipeline {
    agent any
    environment {
        DOCKER_IMAGE_NAME = "serhiikalchenko/spring-petclinic-image"
    }
    stages {
        /*
        stage('Build App') {
            steps {
                echo 'Running build automation'
                sh './mvnw package'
            }
        }
        */
        stage('Build Docker Image') {
            when {
                branch 'main'
            }
            steps {
                script {
                    app = docker.build("$DOCKER_IMAGE_NAME")
                    /*
                    app.inside {
                        sh 'echo $(curl localhost:8080)'
                    }
                    */
                }
            }
        }
        
        stage('Push Docker Image') {
            when {
                branch 'main'
            }
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub_creds') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
        
        stage('Deploy To Kubernetes') {
            when {
                branch 'main'
            }
            steps {
                script{
                    def image = "$DOCKER_IMAGE_NAME" + ":$BUILD_NUMBER"
                     
                    sh "ansible-playbook deploy_to_kubernetes.yml --extra-vars \"DOCKER_IMAGE_NAME_BUILD_NUMBER=${image}\""
                }
            }
        }
    }
}
