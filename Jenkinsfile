pipeline {
    agent any

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
                sh 'docker image build -t alextsydevops/spring-petclinic:v$BUILD_NUMBER'
                sh 'docker image ls -la'
            }
        }
        }
}
