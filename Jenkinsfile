pipeline {
    agent any
    environment {
        SONARQUBE_TOKEN = 'sqa_2479a7ae3cf5d146bc7ed69a88f78e1ba06ab1a2'
    }
    stages {
        stage('SCM Checkout'){
            steps {
                git branch: 'main', url: 'https://github.com/shobana561994/Microservices.git'  
                sh 'ls'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                parallel (
                    'node application': {
                        script {
                            dir('cart-microservice-nodejs') {
                                def scannerHome = tool 'sonarscanner4'
                                withSonarQubeEnv('sonar-pro') {
                                    sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=cart-nodejs -Dsonar.login=${env.SONARQUBE_TOKEN}"
                                }
                            }
                            dir('ui-web-app-reactjs') {
                                def scannerHome = tool 'sonarscanner4';
                                withSonarQubeEnv('sonar-pro') {
                                    sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=ui-reactjs -Dsonar.login=${env.SONARQUBE_TOKEN}"
                                }
                            }
                        }
                    },
                    'spring boot application': {
                        script {
                            dir('offers-microservice-spring-boot') {
                                def mvn = tool 'maven3';
                                withSonarQube
