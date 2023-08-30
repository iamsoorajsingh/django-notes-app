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
         stage("delete_old_images") {
            steps {
               def dockerImages = sh(script: 'docker images --format "{{.ID}}:{{.Repository}}"', returnStdout: true).trim().split('\n')
                if (dockerImages.size() > 3) {
                        def imagesToDelete = dockerImages[3..-1]
                        
                        for (image in imagesToDelete) {
                            def imageParts = image.split(':')
                            def imageId = imageParts[0]
                            def repository = imageParts[1]
                            
                            sh "docker rmi -f $imageId"
                            echo "Deleted image: $repository"
                        }
                    } else {
                        echo "No images to delete."
                    }
            }
        }
    }
}
