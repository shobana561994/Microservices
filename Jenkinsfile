 pipeline {
  agent any
   // environment {
     //   SONARQUBE_TOKEN = 'sqa_2479a7ae3cf5d146bc7ed69a88f78e1ba06ab1a2'
    //}
    stages {
        stage('SCM Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/shobana561994/Microservices.git'
                sh 'ls' // List files to verify checkout
            }
        }
    }
}
