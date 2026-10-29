pipeline {
    agent { label 'devops1-bagus' }

    stages {
        stage('Pull SCM') {
            steps {
                git branch: 'main', url: 'https://github.com/bagusambpi/simple-apps.git'
            }
        }
        
        stage('Build') {
            steps {
                sh '''
                cd apps
                npm install
                '''
            }
        }

        stage('Set ENV') {
            steps {
                withCredentials ([
                    string(credentialsId: 'db-name', variable: 'DB_NAME'),
                    string(credentialsId: 'db-user', variable: 'DB_USER'),
                    string(credentialsId: 'db-password', variable: 'DB_PASSWORD'),
                    string(credentialsId: 'db-host', variable: 'DB_HOST'),
                    string(credentialsId: 'app-port', variable: 'APP_PORT')
                ]) {
                    script {
                        // Membuat file .env di dalam folder apps agar terbaca oleh aplikasi & docker
                        writeFile file: 'apps/.env', text: """
                        DB_NAME=${DB_NAME} 
                        DB_HOST=${DB_HOST} 
                        DB_USER=${DB_USER} 
                        DB_PASS=${DB_PASSWORD} 
                        APP_PORT=${APP_PORT} 
                        """.stripIndent()
                    }
                }
            }
        }
        
        stage('Testing') {
            steps {
                sh '''
                cd apps
                npm run test:coverage
                '''
            }
        }
        
        stage('Code Review') {
            steps {
                sh '''
                cd apps
                sonar-scanner \
                    -Dsonar.projectKey=simple-apps \
                    -Dsonar.sources=. \
                    -Dsonar.host.url=http://172.23.13.83:9000 \
                    -Dsonar.token=sqp_2cd63b07a7f7e1dbd14a891574f49c7c3f325500
                '''
            }
        }
        
        stage('Deploy') {
            steps {
                // Jika untuk docker compose Anda menggunakan host khusus container, 
                // ubah credentialsId-nya ke 'db-host-container'
                withCredentials ([
                    string(credentialsId: 'db-name', variable: 'DB_NAME'),
                    string(credentialsId: 'db-user', variable: 'DB_USER'),
                    string(credentialsId: 'db-password', variable: 'DB_PASSWORD'),
                    string(credentialsId: 'db-host-container', variable: 'DB_HOST'),
                    string(credentialsId: 'app-port', variable: 'APP_PORT')
                ]) {
                    script {
                        // Timpa file .env khusus untuk container jika host-nya berbeda
                        writeFile file: 'apps/.env', text: """
                        DB_NAME=${DB_NAME} 
                        DB_HOST=${DB_HOST} 
                        DB_USER=${DB_USER} 
                        DB_PASS=${DB_PASSWORD} 
                        APP_PORT=${APP_PORT} 
                        """.stripIndent()
                    }
                    
                    sh '''
                    cd apps
                    docker compose up --build -d
                    '''
                }
            }
        }
    }
}