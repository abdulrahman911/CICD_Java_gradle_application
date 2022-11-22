pipeline{
    agent any
    stages{
        stage("sonar quality check"){
            agent {
                docker {
                    image 'openjdk:11'
                }
            }
            steps{
                echo "========executing sonarqube env========"
                script{
                    withSonarQubeEnv(credentialsId: 'sonar-token') {
                    // some block
                        sh 'chmod +x gradlew'
                        sh './gradlew sonarqube'
                    }
                }
            }

        }
    }
}