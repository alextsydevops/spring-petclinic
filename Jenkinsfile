pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('alextsydevops-dockerhub-credentials')
        DOCKER_TAG = getDockerTagVersion()
        DOCKER_HOST_PORT = "8081"
    }

    stages {
        stage('CHECKOUT') {
            steps {
                git branch: 'main', url: 'https://github.com/alextsydevops/spring-petclinic'
            }
        }
        stage('BUILD') {
            steps {
                sh "./mvnw package"
            }
        }
        stage('CREATE ARTIFACT') {
            steps {
                sh 'docker image build -t alextsydevops/spring-petclinic:latest -t alextsydevops/spring-petclinic:${DOCKER_TAG} .'
                withCredentials([string(credentialsId: 'alextsydevops-dockerhub-credentials', variable: 'DOCKER_HUB_PASSWORD')]) {
                    sh 'docker login -u alextsydevops -p ${DOCKER_HUB_PASSWORD}'
                }
                sh 'docker push alextsydevops/spring-petclinic:${DOCKER_TAG}'
            }
        }
        stage('DEPLOY') {
            steps {
                ansiblePlaybook credentialsId: 'java-spring-app-dev-server', disableHostKeyChecking: true, extras: '-e DOCKER_TAG="${DOCKER_TAG}" DOCKER_HOST_PORT="${DOCKER_HOST_PORT}"', installation: 'Ansible', inventory: 'inventory.inv', playbook: 'deploy-app.yml'
            }
        }
        }
        post {
            always {
                sh 'docker logout'
                sh 'docker image rm alextsydevops/spring-petclinic:${DOCKER_TAG}'
            }
        }
}

def getDockerTagVersion(){
    def commitHash = sh returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}