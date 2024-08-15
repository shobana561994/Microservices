pipeline {
    agent any
    environment {
        SONARQUBE_TOKEN = 'sqa_502efbb81bbb90c27eff9cb688f5847bb127eb16'
    }
    stages {
        stage('SCM Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/shobana561994/Microservices.git'
                sh 'ls' // List files to verify checkout
            }
        }
        stage('SonarQube Analysis') {
            parallel {
                stage('Node Application') {
                    steps {
                        script {
                            dir('cart-microservice-nodejs') {
                                def scannerHome = tool 'sonarscanner4'
                                withSonarQubeEnv('sonar-pro') {
                                    sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=cart-nodejs -Dsonar.login=${env.SONARQUBE_TOKEN}"
                                }
                            }
                            dir('ui-web-app-reactjs') {
                                def scannerHome = tool 'sonarscanner4'
                                withSonarQubeEnv('sonar-pro') {
                                    sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=ui-reactjs -Dsonar.login=${env.SONARQUBE_TOKEN}"
                                }
                            }
                        }
                    }
                }
                stage('Spring Boot Application') {
                    steps {
                        script {
                            def mvn = '/usr/bin/mvn'
                            dir('offers-microservice-spring-boot') {
                                sh "which mvn" // Check if mvn is in the PATH
                                sh "${mvn} -version" // Print Maven version to ensure it's found
                                withSonarQubeEnv('sonar-pro') {
                                    sh "${mvn} clean verify sonar:sonar -Dsonar.projectKey=offers-spring-boot -Dsonar.projectName=offers-spring-boot -Dsonar.login=${env.SONARQUBE_TOKEN}"
                                }
                            }
                            dir('shoes-microservice-spring-boot') {
                                sh "which mvn" // Check if mvn is in the PATH
                                sh "${mvn} -version" // Print Maven version to ensure it's found
                                withSonarQubeEnv('sonar-pro') {
                                    sh "${mvn} clean verify sonar:sonar -Dsonar.projectKey=shoe-spring-boot -Dsonar.projectName=shoes-spring-boot -Dsonar.login=${env.SONARQUBE_TOKEN}"
                                }
                            }
                            dir('zuul-api-gateway') {
                                sh "which mvn" // Check if mvn is in the PATH
                                sh "${mvn} -version" // Print Maven version to ensure it's found
                                withSonarQubeEnv('sonar-pro') {
                                    sh "${mvn} clean verify sonar:sonar -Dsonar.projectKey=zuul-api -Dsonar.projectName=zuul-api -Dsonar.login=${env.SONARQUBE_TOKEN}"
                                }
                            }
                        }
                    }
                }
                stage('Python App') {
                    steps {
                        script {
                            dir('wishlist-microservice-python') {
                                def scannerHome = tool 'sonarscanner4'
                                withSonarQubeEnv('sonar-pro') {
                                    sh """${scannerHome}/bin/sonar-scanner \
                                    -Dsonar.projectVersion=1.0-SNAPSHOT \
                                    -Dsonar.sources=. \
                                    -Dsonar.login=${env.SONARQUBE_TOKEN} \
                                    -Dsonar.projectKey=project \
                                    -Dsonar.projectName=wishlist-py \
                                    -Dsonar.inclusions=index.py \
                                    -Dsonar.sourceEncoding=UTF-8 \
                                    -Dsonar.language=python \
                                    -Dsonar.host.url=http://15.206.210.194:9000/"""
                                }
                            }
                        }
                    }
                }
            }
        }
        stage('Build Docker Image and Push') {
            parallel {
                stage('Docker Login') {
                    steps {
                        withCredentials([string(credentialsId: 'dockerpass', variable: 'dockerPassword')]) {
                            sh "echo ${dockerPassword} | docker login -u shobana56it --password-stdin"
                        }
                    }
                }
                stage('UI Web App ReactJS') {
                    steps {
                        script {
                            dir('ui-web-app-reactjs') {
                                sh """
                                export DOCKER_BUILDKIT=1
                                docker build -t shobana56it/ui:v1 .
                                docker push shobana56it/ui:v1
                                docker rmi shobana56it/ui:v1
                                """
                            }
                        }
                    }
                }
                stage('Zuul API Gateway') {
                    steps {
                        script {
                            dir('zuul-api-gateway') {
                                sh """
                                docker build -t shobana56it/api:v1 .
                                docker push shobana56it/api:v1
                                docker rmi shobana56it/api:v1
                                """
                            }
                        }
                    }
                }
                stage('Offers Microservice Spring Boot') {
                    steps {
                        script {
                            dir('offers-microservice-spring-boot') {
                                sh """
                                docker build -t shobana56it/spring:v1 .
                                docker push shobana56it/spring:v1
                                docker rmi shobana56it/spring:v1
                                """
                            }
                        }
                    }
                }
                stage('Shoes Microservice Spring Boot') {
                    steps {
                        script {
                            dir('shoes-microservice-spring-boot') {
                                sh """
                                docker build -t shobana56it/spring:v2 .
                                docker push shobana56it/spring:v2
                                docker rmi shobana56it/spring:v2
                                """
                            }
                        }
                    }
                }
                stage('Cart Microservice NodeJS') {
                    steps {
                        script {
                            dir('cart-microservice-nodejs') {
                                sh """
                                docker build -t shobana56it/ui:v2 .
                                docker push shobana56it/ui:v2
                                docker rmi shobana56it/ui:v2
                                """
                            }
                        }
                    }
                }
                stage('Wishlist Microservice Python') {
                    steps {
                        script {
                            dir('wishlist-microservice-python') {
                                sh """
                                docker build -t shobana56it/python:v1 .
                                docker push shobana56it/python:v1
                                docker rmi shobana56it/python:v1
                                """
                            }
                        }
                    }
                }
            }
        }
        stage ('Deploy on k8s'){
            steps{
                parallel (
                    'deploy on k8s': {
                        script {
                            sh """
                            kubectl create ns ms
                            kubectl config set-context --current --namespace=ms
                            """
                            withKubeCredentials(kubectlCredentials: [[ credentialsId: 'k8s', namespace: 'ms' ]]) {
                                sh 'kubectl get ns' 
                                sh 'kubectl apply -f kubernetes/yamlfile'
                            }
                        }
                    },
                    'kubernetes':{
                         dir('kubernetes'){
                            sh 'kubectl apply -f yamlfile'
                            sh 'kubectl get pods -o wide'
                            sh 'kubectl get svc'
                         }
                    }
                )
            }                
        }
    }
}
