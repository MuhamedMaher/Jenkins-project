pipeline {
    environment {
        dockerproxyImage = ""
        proxyimagename = "mohamedmaher77/proxy:latest"
        dockerbackendImage = ""
        backendimagename = "mohamedmaher77/backend:latest"
        registryCredential = 'Docker'  // Credential ID for Docker Hub login
    }
    agent {
        docker {
            image 'docker:20.10.7-dind' // Use Docker-in-Docker image
            args '--privileged'         // Privileged mode is required for DinD
        }
    }
    stages {
        stage('Checkout Source') {
            steps {
                git branch: 'main', url: 'https://github.com/MuhamedMaher/Jenkins-project.git'
            }
        }

        stage('Build Images') {
            steps {
                script {
                    def dockerproxyImage = docker.build("${proxyimagename}", "./Dockerfiles/nginx")
                    def dockerbackendImage = docker.build("${backendimagename}", "./Dockerfiles/backend")
                }
            }
        }

        stage('Pushing Images to Docker Hub') {
            steps {
                script {
                    echo "Pushing image: ${proxyimagename}"
                    echo "Pushing image: ${backendimagename}"
                    docker.withRegistry('https://registry.hub.docker.com', registryCredential) {
                        dockerproxyImage.push("latest")
                        dockerbackendImage.push("latest")
                    }
                }
            }
        }

        stage('Deploying to Kubernetes') {
            steps {
                script {
                    kubernetesDeploy(configs: 'backend/backend_deployment.yaml,backend/backend_service.yaml,proxy/proxy_deployment.yaml,proxy/proxy_nodeport.yaml,database/db_service.yaml,database/database_deployment.yaml,volumes/db-data-pv.yaml,volumes/db-data-pvc.yaml,volumes/db-secret.yaml')
                }
            }
        }
    }
}

