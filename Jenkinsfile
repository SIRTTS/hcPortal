pipeline {
    agent {
        docker {
            image 'maven:3.5-jdk-8'
            args '-v /root/.m2:/root/.m2'
        }
    }
    stages {
        stage ('Start') {
            steps {
                slackSend (channel: '#hcportal' ,color: '#FFFF00', message: "STARTED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean compile'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Package'){
            steps {
                sh 'mvn -Pprod -Dmaven.test.skip=true package'
            }
        }
    }
    post {
        success {
            archive 'target/*.war*'
            publishHTML(target: [
                            allowMissing: false,
                            alwaysLinkToLastBuild: false,
                            keepAll: true,
                            reportDir : 'target/test-results/coverage/jacoco',
                            reportFiles: 'index.html',
                            reportName: 'Code Coverage Report'
            ])
            slackSend (channel: '#hcportal' ,color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
        }

        failure {
            slackSend (channel: '#hcportal' ,color: '#FF0000', message: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
        }

        always {
            junit 'target/surefire-reports/*.xml'
            echo "Send notifications for result: ${currentBuild.result}"
        }
    }
}
