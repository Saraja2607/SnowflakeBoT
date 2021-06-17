pipeline {
    agent any

    stages {
        stage('Checkout Git Branch') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Saraja2607/SnowflakeBoT.git']]])
            }
        }
        stage('Build') {
            steps {
                git branch: 'main', url: 'https://github.com/Saraja2607/SnowflakeBoT.git'
                bat 'python myFile.py'
            }
        }
        stage('Test') {
            steps {
                echo 'This job has been tested'
            }
        }
    }
}
