pipeline {
    agent any

    triggers {
        pollSCM('H/5 * * * *') // Vérifie le repo toutes les 5 minutes
    }

    environment {
        IMAGE_SERVER = 'yasmin952/mern-server'   // À modifier : ton DockerHub username
        IMAGE_CLIENT = 'yasmin952/mern-client'   // À modifier : ton DockerHub username
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'git@gitlab.com:JammeliYasmin/tp2-devops.git',
                    credentialsId: 'gitlab_ssh' // À modifier
            }
        }

        stage('Build + Push SERVER') {
            when { changeset "server/**" }
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'gitlab_ssh',    // À modifier selon ton ID Jenkins
                    usernameVariable: 'DH_USER',
                    passwordVariable: 'DH_PASS'
                )]) {     
                    sh '''
                        echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin
                        docker build -t $IMAGE_SERVER:${BUILD_NUMBER} server
                        # Scanner l'image immédiatement après le push
                        docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image $IMAGE_SERVER:${BUILD_NUMBER} 
                        docker push $IMAGE_SERVER:${BUILD_NUMBER}
                    '''
                }
            }
        }

        stage('Build + Push CLIENT') {
            when { changeset "client/**" }
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DH_USER',
                    passwordVariable: 'DH_PASS'
                )]) {
                    sh '''
                        echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin
                        docker build -t $IMAGE_CLIENT:${BUILD_NUMBER} client
                        docker push $IMAGE_CLIENT:${BUILD_NUMBER}
                        # Scanner l'image immédiatement après le push
                        docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image $IMAGE_CLIENT:${BUILD_NUMBER} 
                    '''
                }
            }
        }
    }

    post {
        always {
            sh 'docker system prune -af || true'
        }
    }
}
