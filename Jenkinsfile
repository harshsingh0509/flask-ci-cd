pipeline {
    agent any
    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/harshsingh0509/flask-ci-cd.git'
            }
        }
        stage('Install Dependencies') {
            steps {
                sh 'pip install -r requirements.txt'  // Flask
                sh 'npm install'  // Node.js
            }
        }
        stage('Deploy App') {
            steps {
                sh 'nohup python3 app.py &'  // Flask
                sh 'nohup node index.js &'  // Node.js
            }
        }
    }
}
