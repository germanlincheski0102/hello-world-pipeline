pipeline {
    agent {
	label 'Debian'
    }	
    options {
        disableConcurrentBuilds()
        timeout(time: 10, unit: 'MINUTES')

    stages {
        stage('Test') {
            steps {
                sh 'mvn -f my-app/ test'
            }
        }

	    stage ('Build') {
            steps {
                sh 'mvn -f my-app/ package'
           }
        }

	    stage('cat README') {
      	    when {
        	    branch "feature-*"
      		 }
            steps {
                sh 'cat README.md'
                  }
        }

	    stage ('Sonar analysis') {
            steps {
                echo '### Executing Sonar analysis ###'
               withSonarQubeEnv(installationName: 'sonar_scanner', credentialsId: 'SonarQubeToken') {
                sh 'mvn -f my-app/ clean package sonar:sonar'
                }
            }
        }

        stage('Quality Gate') {

            steps {
                script {
                    echo '### Quality Gate ###'
                    timeout(time: 5, unit: 'MINUTES') {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            echo '### NOT OK! ###'
                            echo "Pipeline is UNSTABLE due to quality gate failure: ${qg.status}"
                            currentBuild.result = 'UNSTABLE'
                        }
                    }
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