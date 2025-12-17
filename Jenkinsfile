pipeline {
    agent any
    
    triggers { 
        pollSCM('H/5 * * * *') 
    }
    
    environment {
        IMAGE_SERVER = 'youruser/mern-server' // TODO: Changez avec votre username Docker Hub
        IMAGE_CLIENT = 'youruser/mern-client' // TODO: Changez avec votre username Docker Hub
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'git@gitlab.com:JammeliYasmin/tp2-devops.git', // TODO: Changez avec votre repo
                    credentialsId: 'gitlab_ssh'
            }
        }
        
        stage('Build + Push SERVER') {
            when { 
                changeset 'server/**' 
            }
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DH_USER',
                    passwordVariable: 'DH_PASS'
                )]) {
                    sh '''
                        echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin
                        docker build -t $IMAGE_SERVER:${BUILD_NUMBER} server
                        docker push $IMAGE_SERVER:${BUILD_NUMBER}
                    '''
                }
            }
        }
        
        stage('Scan SERVER with Trivy') {
            when { 
                changeset 'server/**' 
            }
            steps {
                sh '''
                    docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \
                        aquasec/trivy image $IMAGE_SERVER:${BUILD_NUMBER} > trivy_server_report.txt
                    cat trivy_server_report.txt
                '''
                archiveArtifacts artifacts: 'trivy_server_report.txt', allowEmptyArchive: true
            }
        }
        
        stage('Build + Push CLIENT') {
            when { 
                changeset 'client/**' 
            }
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
                    '''
                }
            }
        }
        
        stage('Scan CLIENT with Trivy') {
            when { 
                changeset 'client/**' 
            }
            steps {
                sh '''
                    docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \
                        aquasec/trivy image $IMAGE_CLIENT:${BUILD_NUMBER} > trivy_client_report.txt
                    cat trivy_client_report.txt
                '''
                archiveArtifacts artifacts: 'trivy_client_report.txt', allowEmptyArchive: true
            }
        }
    }
    
    post {
        always {
            sh 'docker system prune -af || true'
        }
    }
}