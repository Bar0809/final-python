pipeline {
    agent {
        label 'docker'
    }

    stages {
        stage('Checkout from git') {
            steps {
                checkout([$class: 'GitSCM',
                          branches: [[name: '*/main']],
                          userRemoteConfigs: [[url: 'https://github.com/Bar0809/final-python']]])
            }
        }

        stage('Build') {
            steps {
                sh "docker build -t final-python:${env.BUILD_NUMBER} ."
            }
        }

        stage('Test') {
            steps {
                sh "docker run --name final-python -d -p 8081:5000 final-python:${env.BUILD_NUMBER}"
                sh "sleep 5"
                sh "curl http:/localhost:8081/api/doc"
                sh 'docker stop final-python && docker rm final-python'
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials(bindings:[usernamePassword(credentialsId: 'docker-hub', passwordVariable: 'pass', usernameVariable: 'user')]) {
                    sh "docker tag final-python:${env.BUILD_NUMBER} baresther/final-python:${env.BUILD_NUMBER}"
                    sh "docker login -u $user -p $pass "
                    sh "docker push baresther/final-python:${env.BUILD_NUMBER}"
                }
            }
 }
       
    }
}
