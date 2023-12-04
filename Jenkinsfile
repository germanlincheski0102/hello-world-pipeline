pipeline {
    agent {
	label 'Debian'
    }	
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/germanlincheski0102/hello-world-pipeline.git'
                sh 'mvn -f my-app/ clean install'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn -f my-app/ test'
            }
        }

	stage ('Build') {
            steps {
                sh 'mvn -f my-app/ package'
           }

	stage('cat README') {
      	    when {
        	branch "feature-*"
      		 }
            steps {
                sh '''
                  cat README.md
                  '''
                  }
                }
        }

	stage ('Scan and Build Jar File') {
            steps {
               withSonarQubeEnv(installationName: 'sonar_scanner', credentialsId: 'SonarQubeToken') {
                sh 'mvn -f my-app/ clean package sonar:sonar'
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying...'
            }
        }
    }
}
