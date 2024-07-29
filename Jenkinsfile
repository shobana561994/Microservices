pipeline {
    agent any
    stages {
        stage('SCM Checkout') {
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
                                    sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=cart-nodejs"
                                }
                            }
                            dir('ui-web-app-reactjs') {
                                def scannerHome = tool 'sonarscanner4'
                                withSonarQubeEnv('sonar-pro') {
                                    sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=ui-reactjs"
                                }
                            }
                        }
                    },
                    'spring boot application': {
                        script {
                            dir('offers-microservice-spring-boot') {
                                def mvn = tool 'maven3'
                                withSonarQubeEnv('sonar-pro') {
                                    sh "${mvn}/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=offers-spring-boot -Dsonar.projectName=offers-spring-boot"
                                }
                            }
                            dir('shoes-microservice-spring-boot') {
                                def mvn = tool 'maven3'
                                withSonarQubeEnv('sonar-pro') {
                                    sh "${mvn}/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=shoe-spring-boot -Dsonar.projectName=shoes-spring-boot"
                                }
                            }
                            dir('zuul-api-gateway') {
                                def mvn = tool 'maven3'
                                withSonarQubeEnv('sonar-pro') {
                                    sh "${mvn}/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=zuul-api -Dsonar.projectName=zuul-api"
                                }
                            }
                        }
                    },
                    'python app': {
                        script {
                            dir('wishlist-microservice-python') {
                                def scannerHome = tool 'sonarscanner4'
                                withSonarQubeEnv('sonar-pro') {
                                    sh """
                                        ${scannerHome}/bin/sonar-scanner \
                                        -Dsonar.projectVersion=1.0-SNAPSHOT \
                                        -Dsonar.sources=. \
                                        -Dsonar.login=${SONAR_LOGIN} \
                                        -Dsonar.password=${SONAR_PASSWORD} \
                                        -Dsonar.projectKey=wishlist-py \
                                        -Dsonar.projectName=wishlist-py \
                                        -Dsonar.inclusions=index.py \
                                        -Dsonar.sourceEncoding=UTF-8 \
                                        -Dsonar.language=python \
                                        -Dsonar.host.url=http://43.204.232.8:9000/
                                    """
                                }
                            }
                        }
                    }
                )
            }
        }
        stage('Build Docker Image and Push') {
            steps {
                parallel (
                    'docker login': {
                        withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
                            sh "docker login -u shobana56it -p ${dockerPassword}"
                        }
                    },
                    'ui-web-app-reactjs': {
                        dir('ui-web-app-reactjs') {
                            sh """
                                docker build -t shobana56it/ui:v1 .
                                docker push shobana56it/ui:v1
                                docker rmi shobana56it/ui:v1
                            """
                        }
                    },
                    'zuul-api-gateway': {
                        dir('zuul-api-gateway') {
                            sh """
                                docker build -t shobana56it/api:v1 .
                                docker push shobana56it/api:v1
                                docker rmi shobana56it/api:v1
                            """
                        }
                    },
                    'offers-microservice-spring-boot': {
                        dir('offers-microservice-spring-boot') {
                            sh """
                                docker build -t shobana56it/spring:v1 .
                                docker push shobana56it/spring:v1
                                docker rmi shobana56it/spring:v1
                            """
                        }
                    },
                    'shoes-microservice-spring-boot': {
                        dir('shoes-microservice-spring-boot') {
                            sh """
                                docker build -t shobana56it/spring:v2 .
                                docker push shobana56it/spring:v2
                                docker rmi shobana56it/spring:v2
                            """
                        }
                    },
                    'cart-microservice-nodejs': {
                        dir('cart-microservice-nodejs') {
                            sh """
                                docker build -t shobana56it/ui:v2 .
                                docker push shobana56it/ui:v2
                                docker rmi shobana56it/ui:v2
                            """
                        }
                    },
                    'wishlist-microservice-python': {
                        dir('wishlist-microservice-python') {
                            sh """
                                docker build -t shobana56it/python:v1 .
                                docker push shobana56it/python:v1
                                docker rmi shobana56it/python:v1
                            """
                        }
                    }
                )
            }
        }
        stage('Deploy on Kubernetes') {
            steps {
                script {
                    withKubeCredentials(kubeconfigId: 'k8s', namespace: 'ms') {
                        sh 'kubectl get ns'
                        sh 'kubectl apply -f kubernetes/yamlfile'
                    }
                }
            }
        }
    }
}
