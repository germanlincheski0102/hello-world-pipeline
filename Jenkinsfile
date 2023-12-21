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
        APP_NAME = "hello-world-app"
    }

    stages {
        stage('Test') {
            when {
                anyOf{
                    branch "feature*";
                    branch "dev";
                }
            }
            steps {
                sh 'mvn -f ${APP_NAME} test'
            }
        }

	    stage ('Build & Sonar analysis') {
            when {
                anyOf{
                    branch "feature-*";
                    branch "dev";
                }
            }
            steps {
                echo '### Executing Static Sonar analysis ###'
                withSonarQubeEnv(installationName: 'sonar_scanner', credentialsId: 'SonarQubeToken') {
                sh 'mvn -f ${APP_NAME} clean package sonar:sonar -Pcoverage \
                -Dsonar.projectKey= ${APP_NAME} \
                -Dsonar.projecName=${APP_NAME}_${BRANCH_NAME}_${BUILD_NUMBER}'
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
                            error "Pipeline aborted due to quality gate failure: ${qg.status}"
                        }
                    }
                    echo 'Quality Gate Passed'
                }
            }
        }
        
        stage('Build and push Docker image') {
            when {
                anyOf{
                    branch 'dev';
                    branch 'release/*';
                }
	        }	
            steps {
                script {
		            echo 'Building Docker image'
                    /*docker build -t ${GIT_REPO} .
                    echo AWS_ACCOUNT=\$AWS_ACCOUNT
                    docker tag ${GIT_REPO}:latest
                    docker push */
                }
            }
        }

        stage('Deploy') {
            when {
                anyOf{
                    branch 'main'
                }
            }
            steps {
                script {
                    echo 'Deploying...'
            }
        }
      }
    }

    post { 
        always {
        echo 'Enviando mail de reporte' }
    }
}
