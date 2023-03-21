pipeline {
    agent any
    environment{
        VERSION = "${env.BUILD_ID}"
    }
    stages {
        stage("Sonar Quality Check") {
            // agent {
            //     docker {
            //         image 'openjdk:11'
            //     }
            // }
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'sonar-token') {
                    sh 'chmod +x gradlew'
                    sh './gradlew sonarqube'
                    }
                    // this is a timeout script section from sonarqube quality gate check
                    timeout(time: 1, unit: 'HOURS') {
                    def qg = waitForQualityGate()
                    if (qg.status != 'OK') {
                        error "Pipeline aborted due to quality gate failure: ${qg.status}"
                      }
                    }
                }
            }

        }
        stage("Docker build & Docker Image push"){
            steps{
                script {
                    withCredentials([string(credentialsId: 'docker-pass', variable: 'docker_password')]) {
                        
                        sh '''
                            docker build -t 43.204.100.151:8083/springapp:${VERSION} .
                            docker login -u admin -p $docker_password 43.204.100.151:8083
                            docker push 43.204.100.151:8083/springapp:${VERSION}
                            docker rmi 43.204.100.151:8083/springapp:${VERSION}

                        '''
                    }
                }
            }
        }
        stage('Identyfying misconfigs using datree in helms charts'){
            steps{
                script {
                    dir('kubernetes') {
                        // some block
                        withEnv(['DATREE_TOKEN=ad8be631-4f91-4259-a9e4-fe1fc9dbfd24']) {
                        // some block
                            //sh 'helm plugin install https://github.com/datreeio/helm-datree'
                            sh 'helm datree test myapp/'
                        }
                    }
                }
            }
        }
    }
    post {
		always {
			mail bcc: '', body: "<br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "${currentBuild.result} CI: Project name -> ${env.JOB_NAME}", to: "abduldevops91@gmail.com";  
		}
	}

}