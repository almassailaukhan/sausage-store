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
            curl -x http://192.168.188.103:3128 -4 --max-time 10 -s -X POST -H "Content-type: application/json" \
            --data '{"chat_id": "439902278", "text": "✅ Build SUCCESS"}' \
            https://api.telegram.org/bot8327046113:AAHiB88gBX_LrH0kbVHZOG0EIhE-5LuxzJw/sendMessage || true
            '''
        }
        failure {
            sh '''
            curl -x http://192.168.188.103:3128 -4 --max-time 10 -s -X POST -H "Content-type: application/json" \
            --data '{"chat_id": "439902278", "text": "❌ Build FAILED"}' \
            https://api.telegram.org/bot8327046113:AAHiB88gBX_LrH0kbVHZOG0EIhE-5LuxzJw/sendMessage || true
            '''
        }
    }
}
