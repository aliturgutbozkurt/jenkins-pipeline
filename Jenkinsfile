pipeline {
    environment {
        registry = "aliturgutbozkurt/jenkins-docker-test"
    }
    options {
        skipStagesAfterUnstable()
    }
    agent {
        docker {
            image 'circleci/node'
            args  '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }
    stages {
        stage("Build"){
            steps {
                sh 'npm install'
            }
        }
        stage("Test"){
            steps {
                sh 'npm test'
            }
        }
        stage("Build & Push Docker image") {
            steps {
                sh 'docker image build -t $registry:$BUILD_NUMBER .'
                withCredentials([usernamePassword(credentialsId: 'docker-login-pwd', passwordVariable: 'DOCKER_REGISTRY_PWD', usernameVariable: 'DOCKER_REGISTRY_USER')]){
                    sh 'docker login -u DOCKER_REGISTRY_USER -p $DOCKER_REGISTRY_PWD'
                }    
                sh 'docker image push $registry:$BUILD_NUMBER'
                sh "docker image rm $registry:$BUILD_NUMBER"
            }
        }
        stage('Deploy and smoke test') {
            steps{
                sh './jenkins/scripts/deploy.sh'
            }
        }
        stage('Cleanup') {
            steps{
                sh './jenkins/scripts/cleanup.sh'
            }
        }
    }
}
