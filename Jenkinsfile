pipeline {
    agent any
    environment {
        SONARQUBE_TOKEN = 'sqa_374517fd07b396494c27f0552d61369653335ba5'
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
                                    sh "/usr/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=offers-spring-boot -Dsonar.projectName=offers-spring-boot -Dsonar.login=${env.SONARQUBE_TOKEN}"
                                }
                            }
                            dir('shoes-microservice-spring-boot') {
                                sh 'which mvn' // Check if mvn is in the PATH
                                sh '/usr/bin/mvn -version' // Print Maven version to ensure it's found
                                withSonarQubeEnv('sonar-pro') {
                                    sh "/usr/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=shoe-spring-boot -Dsonar.projectName=shoes-spring-boot -Dsonar.login=${env.SONARQUBE_TOKEN}"
                                }
                            }
                            dir('zuul-api-gateway') {
                                sh 'which mvn' // Check if mvn is in the PATH
                                sh '/usr/bin/mvn -version' // Print Maven version to ensure it's found
                                withSonarQubeEnv('sonar-pro') {
                                    sh "/usr/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=zuul-api -Dsonar.projectName=zuul-api -Dsonar.login=${env.SONARQUBE_TOKEN}"
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
                                    -D sonar.host.url=http://13.235.86.77:9000/""" 
                                }
                            }
                        }
                    }
                )
            }
        }
        stage ('Build Docker Image and Push') {
            steps {
                parallel (
                    'docker login': {
                        
                        withCredentials([usernamePassword(credentialsId: 'dockerPass', usernameVariable: 'shobana56it', passwordVariable: 'Shob@n@561994')]) {
                            sh "docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}"
                        
                        //withCredentials([string(credentialsId: 'dockerpass', variable: 'dockerPassword')]) {
                          //  sh "docker login -u shobana56it -p ${dockerPassword}"
                        }
                    },
                    'ui-web-app-reactjs': {
                        dir('ui-web-app-reactjs') {
                            sh """
                            docker build -t shobana56it/ui:v1 . || true
                            docker push shobana56it/ui:v1 || true
                            docker rmi shobana56it/ui:v1 || true
                            """
                        }
                    },
                    'zuul-api-gateway' : {
                        dir('zuul-api-gateway') {
                            sh """
                            docker build -t shobana56it/api:v1 . || true
                            docker push shobana56it/api:v1 || true
                            docker rmi shobana56it/api:v1 || true
                            """
                        }
                    },
                    'offers-microservice-spring-boot': {
                        dir('offers-microservice-spring-boot') {
                            sh """
                            docker build -t shobana56it/spring:v1 . || true
                            docker push shobana56it/spring:v1 || true
                            docker rmi shobana56it/spring:v1 || true
                            """
                        }
                    },
                    'shoes-microservice-spring-boot': {
                        dir('shoes-microservice-spring-boot') {
                            sh """
                            docker build -t shobana56it/spring:v2 . || true
                            docker push shobana56it/spring:v2 || true
                            docker rmi shobana56it/spring:v2 || true
                            """
                        }
                    },
                    'cart-microservice-nodejs': {
                        dir('cart-microservice-nodejs') {
                            sh """
                            docker build -t shobana56it/ui:v2 . || true
                            docker push shobana56it/ui:v2 || true
                            docker rmi shobana56it/ui:v2 || true
                            """
                        }
                    },
                    'wishlist-microservice-python': {
                        dir('wishlist-microservice-python') {
                            sh """
                            docker build -t shobana56it/python:v1 . || true
                            docker push shobana56it/python:v1 || true
                            docker rmi shobana56it/python:v1 || true
                            """
                        }
                    }
                )
            }
        }
    }
}
