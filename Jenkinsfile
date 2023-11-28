pipeline {
    agent {
	label 'Debian'
    }	
    stages {
        stage('Build') {
            steps {
                git 'https://github.com/germanlincheski0102/hello-world-pipeline.git'
                sh 'mvn -f my-app/ clean install'
		sh 'mvn -f my-app/ package'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn -f my-app/ test'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying...'
            }
        }
    }
}
