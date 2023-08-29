pipeline {
    agent any
    stages {
        stage("code") {
            steps {
                echo "cloning the code"
                git url: "https://github.com/iamsoorajsingh/django-notes-app", branch: "main"
            }
        }
        stage("Build") {
            steps {
                echo "building"
                sh "docker build -t my-note-app ."
            }
        }
        stage("push_to_docker") {
            steps {
                withCredentials([usernamePassword(credentialsId: "docker_cred",
                        passwordVariable: "dockerHubPass",
                        usernameVariable: "dockerHubUser")]) {
                    sh "docker tag my-note-app ${env.dockerHubUser}/my-note-app:v1"
                    sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                    sh "docker push ${env.dockerHubUser}/my-note-app:v1"
                    
                }
            }
        }
        stage("deploy") {
            steps {
                sh "docker-compose down && docker-compose up -d"
            }
        }
    }
}
