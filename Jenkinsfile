pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('alextsydevops-dockerhub-credentials')
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
                sh 'docker image build -t alextsydevops/spring-petclinic:latest -t alextsydevops/spring-petclinic:v$BUILD_NUMBER .'
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                sh 'docker push --all-tags alextsydevops/spring-petclinic'
            }
        }
        }
        post {
            always {
                sh 'docker logout'
            }
        }
}
