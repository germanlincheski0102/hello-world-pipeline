pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                git 'https://github.com/germanlincheski0102/hello-world-pipeline.git'
                sh 'mvn package'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying...'
            }
        }
    }
}
