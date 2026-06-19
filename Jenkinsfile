pipeline {
    agent any // Выбираем Jenkins агента, на котором будет происходить сборка: нам нужен любой

    triggers {
        pollSCM('H/5 * * * *') // Запускать будем автоматически по крону примерно раз в 5 минут
    }

    tools {
        maven 'maven3.9' // Для сборки бэкенда нужен Maven
        jdk 'JDK17' // И Java Developer Kit нужной версии
        nodejs 'node14' // А NodeJS нужен для фронта
    }

    stages {
        stage('Build & Test backend') {
            steps {
                dir("backend") { // Переходим в папку backend
                    sh 'mvn package' // Собираем мавеном бэкенд
                }
            }

            post {
                success {
                    junit 'backend/target/surefire-reports/**/*.xml' // Передадим результаты тестов в Jenkins
                }
            }
        }

        stage('Build frontend') {
            steps {
                dir("frontend") {
                    sh 'npm install' // Для фронта сначала загрузим все сторонние зависимости
                    sh 'npm run build' // Запустим сборку
                }
            }
        }
        stage('Test Telegram') {
            steps {
                sh 'curl -v https://api.telegram.org
                }
            }
        }

        
        stage('Save artifacts') {
            steps {
                archiveArtifacts(artifacts: 'backend/target/sausage-store-0.0.1-SNAPSHOT.jar')
                archiveArtifacts(artifacts: 'frontend/dist/frontend/*')
            }

	    post {
                success {
                   sh '''
                   curl -X POST -H 'Content-type: application/json' \
                   --data '{"chat_id": "439902278", "text": "✅ Build SUCCESS"}' \
                   https://api.telegram.org/bot8327046113:AAHiB88gBX_LrH0kbVHZOG0EIhE-5LuxzJw/sendMessage
                   '''
                }
                failure {
                   sh '''
                   curl -X POST -H 'Content-type: application/json' \
                   --data '{"chat_id": "439902278", "text": "❌ Build FAILED"}' \
                   https://api.telegram.org/bot8327046113:AAHiB88gBX_LrH0kbVHZOG0EIhE-5LuxzJw/sendMessage
                   '''
                }
            }

        }


    }
} 
