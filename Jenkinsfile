pipeline {
    agent any

    triggers {
        pollSCM('H/5 * * * *')
    }

    tools {
        maven 'maven3.9'
        jdk 'JDK17'
        nodejs 'node14'
    }

    stages {
        stage('Build & Test backend') {
            steps {
                dir("backend") {
                    sh 'mvn package'
                }
            }
            post {
                success {
                    junit 'backend/target/surefire-reports/**/*.xml'
                }
            }
        }

        stage('Build frontend') {
            steps {
                dir("frontend") {
                    sh 'npm install'
                    sh 'npm run build'
                }
            }
        }

        stage('Test Telegram') {
            steps {
                sh '''
                curl -v https://api.telegram.org
                '''
            }
        }

        stage('Save artifacts') {
            steps {
                archiveArtifacts artifacts: 'backend/target/sausage-store-0.0.1-SNAPSHOT.jar'
                archiveArtifacts artifacts: 'frontend/dist/frontend/**'
            }
        }
    }

    post {
        success {
            sh '''
            curl --max-time 10 -s -X POST -H "Content-type: application/json" \
            --data '{"chat_id": "439902278", "text": "✅ Build SUCCESS"}' \
            https://api.telegram.org/botTOKEN/sendMessage || true
            '''
        }
        failure {
            sh '''
            curl --max-time 10 -s -X POST -H "Content-type: application/json" \
            --data '{"chat_id": "439902278", "text": "❌ Build FAILED"}' \
            https://api.telegram.org/botTOKEN/sendMessage || true
            '''
        }
    }
}
