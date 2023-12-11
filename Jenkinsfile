pipeline {
    agent {
	label 'Debian'
    }	
    options {
        disableConcurrentBuilds()
        timeout(time: 10, unit: 'MINUTES')
        buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '10', daysToKeepStr: '', numToKeepStr: '10')
    }
    environment {
        GIT_REPO = "hello-world-pipeline"
    }

    stages {
        stage('Test') {
            when {
                anyOf{
                    branch "feature-*";
                    branch "dev";
                }
            }
            steps {
                sh 'mvn -f my-app/ test'
            }
        }

	    stage ('Build') {
            when {
                anyOf{
                    branch "feature-*";
                    branch "dev";
                }
            }
            steps {
                sh 'mvn -f my-app/ package'
           }
        }

	    stage ('Sonar analysis') {
            when {
                anyOf{
                    branch "feature-*";
                    branch "dev";
                }
            }
            steps {
                echo '### Executing Sonar analysis ###'
                withSonarQubeEnv(installationName: 'sonar_scanner', credentialsId: 'SonarQubeToken') {
                sh 'mvn -f my-app/ clean package sonar:sonar \
                -Dsonar.projectKey=${GIT_REPO}'
                }
            }
        }

        stage('Quality Gate') {
            when {
                anyOf{
                    branch "feature-*";
                    branch "dev";
                }
            }
            steps {
                script {
                    echo '### Quality Gate ###'
                    timeout(time: 2, unit: 'MINUTES') {
                        waitForQualityGate abortPipeline: true
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            echo '### NOT OK! ###'
                            echo "Pipeline is UNSTABLE due to quality gate failure: ${qg.status}"
                            currentBuild.result = 'UNSTABLE'
                        }
                    }
                    echo 'Quality Gate Passed'
                }
            }
        }

        stage('Build and push new Docker image') {
            when {
                anyOf{
                    branch 'dev';
                    branch 'release/*';
                }
	        }	
            steps {
		echo 'Building Docker image'
            }
        }

        stage('Deploy') {
            when {
                anyOf{
                    branch 'main';
                    branch 'master';
                }
            }
            steps {
                echo 'Deploying...'
            }
        }
    }
    post { 
        always {
        echo 'Enviando mail de reporte' }
    }
}