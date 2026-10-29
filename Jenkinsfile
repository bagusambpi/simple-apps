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
                sh'''
                cd apps
                npm install
                '''
            }
        }
        
        stage('Testing') {
            steps {
                sh'''
                cd apps
                npm run test:coverage
                '''
            }
        }
        
        stage('Code Review') {
            steps {
                sh'''
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
                sh'''
                docker compose up --build -d
                '''
            }
        }
        
        
    }
}