 pipeline {
    agent any

    stages {
        stage('code checkout') {
            steps {
                git 'https://github.com/Jaya7385/20-maven-webapplication-project-kkfunda.git'
            }
        }

        stage('code compile') {
            steps {
                sh 'mvn compile'
            }
        }

        stage('unit test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('build artifact') {
            steps {
                sh 'mvn clean package'
            }
        }

        /*
        stage('push artifact to nexus') {
            steps {
                sh 'mvn deploy'
            }
        }
        */

        stage('Deploy to Tomcat') {
            steps {
                sh """
                    curl -u kk:password \\
                    --upload-file target/maven-web-application.war \\
                    "http://43.205.57.64:8082/manager/text/deploy?path=/maven-web-application&update=true"
                """
            }
        }
    }

    post {
        success {
            script {
                notifyBuild(currentBuild.result)
            }
        }

        failure {
            script {
                notifyBuild(currentBuild.result)
            }
        }
    }
}

def notifyBuild(String buildStatus = 'STARTED') {
    buildStatus = buildStatus ?: 'SUCCESS'

    def colorCode
    def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
    def summary = "${subject} (${env.BUILD_URL})"

    switch (buildStatus) {
        case 'STARTED':
            colorCode = '#FFFF00'
            break
        case 'SUCCESS':
            colorCode = '#00FF00'
            break
        default:
            colorCode = '#FF0000'
    }

    slackSend(color: colorCode, message: summary)
}
