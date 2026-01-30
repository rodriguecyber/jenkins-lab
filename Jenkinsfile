pipeline {
    agent any

    stages {
        stage('install dependencies') {
            steps {
                echo 'Installing dependencies...'
                sh 'npm install'
            }
            
        }
        
        stage('Test') {
            steps {
                echo 'Testing..'
                sh 'npm test'
            }
        } 
    }

} 