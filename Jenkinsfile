pipeline {
    agent any
    
    stages {
        stage('Build Docker images') {
            steps {
                script {
                    docker.build('mohijeet/client-image', './client')
                    docker.build('mohijeet/server-image', './server')
                    docker.build('mohijeet/worker-image', './worker')
                   
                }
            }
        }
        
        stage('Push Docker images to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
                    script {
                        // Login to Docker Hub
                        sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
                        
                        // Push Docker images to Docker Hub
                        docker.image('mohijeet/client-image:latest').push()
                        docker.image('mohijeet/server-image:latest').push()
                        docker.image('mohijeet/worker-image:latest').push()
                        // Push other Docker images if needed
                    }
                }
            }
        }

         stage('Deploy to EKS') {
            steps {
                withCredentials([aws(credentialsId: 'aws-credentials', region: 'us-east-1')]) {
                    script {
                        // Update kubeconfig to use the EKS cluster
                        sh 'aws eks update-kubeconfig --name your-eks-cluster-name'

                        // Apply all Kubernetes manifests in the k8s folder
                        sh 'kubectl apply -f k8s/'
                    }
                }
            }
        }
    }
}
