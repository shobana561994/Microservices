pipeline {
    agent any
    environment {
        SONARQUBE_TOKEN = 'sqa_2479a7ae3cf5d146bc7ed69a88f78e1ba06ab1a2'
    }
    stages {
        stage('SCM Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/shobana561994/Microservices.git'
                sh 'ls' // List files to verify checkout
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
                                sh 'which mvn' // Check if mvn is in the PATH
                                sh '/usr/bin/mvn -version' // Print Maven version to ensure it's found
                                withSonarQubeEnv('sonar-pro') {
                                    sh "/opt/maven/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=offers-spring-boot -Dsonar.projectName=offers-spring-boot -Dsonar.login=${env.SONARQUBE_TOKEN}"
                                }
                            }
                            dir('shoes-microservice-spring-boot') {
                                sh 'which mvn' // Check if mvn is in the PATH
                                sh '/usr/bin/mvn -version' // Print Maven version to ensure it's found
                                withSonarQubeEnv('sonar-pro') {
                                    sh "/opt/maven/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=shoe-spring-boot -Dsonar.projectName=shoes-spring-boot -Dsonar.login=${env.SONARQUBE_TOKEN}"
                                }
                            }
                            dir('zuul-api-gateway') {
                                sh 'which mvn' // Check if mvn is in the PATH
                                sh '/usr/bin/mvn -version' // Print Maven version to ensure it's found
                                withSonarQubeEnv('sonar-pro') {
                                    sh "/opt/maven/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=zuul-api -Dsonar.projectName=zuul-api -Dsonar.login=${env.SONARQUBE_TOKEN}"
                                }
                            }
                        }
                    },
                    'python app': {
                        script {
                            dir('wishlist-microservice-python') {
                                def scannerHome = tool 'sonarscanner4';
                                withSonarQubeEnv('sonar-pro') {
                                    sh """/var/lib/jenkins/tools/hudson.plugins.sonar.SonarRunnerInstallation/sonarscanner4/bin/sonar-scanner \
                                    -D sonar.projectVersion=1.0-SNAPSHOT \
                                    -D sonar.sources=. \
                                    -D sonar.login=${env.SONARQUBE_TOKEN} \
                                    -D sonar.projectKey=project \
                                    -D sonar.projectName=wishlist-py \
                                    -D sonar.inclusions=index.py \
                                    -D sonar.sourceEncoding=UTF-8 \
                                    -D sonar.language=python \
                                    -D sonar.host.url=http://43.204.232.8:9000/""" 
                                }
                            }
                        }
                    }
                )
            }
        }
    }
}
