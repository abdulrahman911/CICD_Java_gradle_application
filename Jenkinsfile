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
                    withCredentials([string(credentialsId: 'docker-pass', variable: 'docker-password')]) {
                        
                        sh '''
                            docker build -t 43.204.100.151:8083/springapp:${VERSION} .
                            docker login -u admin -p $docker-password 43.204.100.151:8083
                            docker push 43.204.100.151:8083/springapp:${VERSION}
                            docker rmi 43.204.100.151:8083/springapp:${VERSION}

                        '''
                    }
                }
            }
        }
    }

}