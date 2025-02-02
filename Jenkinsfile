pipeline {
    agent any
    
    tools {
        nodejs 'node16'
    }
    // environment{
    //     SCANNER_HOME = tool 'sonar-scanner'
    // }

    stages {
        stage('clean workspace') {
            steps {
                cleanWs()
            }
        }
        
         stage('Checkout Repo') {
            steps {
                git branch: 'deploy', url: 'https://github.com/haleem00/Netflix-clone.git'
            }
        }
        
       
        stage('K8') {
            steps {
                dir('Kubernetes') { // Specify the directory here
                    withKubeCredentials(kubectlCredentials: [[
                    caCertificate: '', 
                    clusterName: 'EKS_CLOUD', 
                    contextName: '', 
                    credentialsId: 'k8-token', 
                    namespace: 'webapps', 
                    serverUrl: 'https://2694E103F67E27D4115E703BA534F8D5.gr7.us-east-1.eks.amazonaws.com'
                    ]]) {
                        sh 'kubectl apply -f deployment.yml'
                        sh 'kubectl apply -f service.yml'
                        }
                }
            }
        }

            
            
    }
    post {
     always {
        emailext attachLog: true,
            subject: "'${currentBuild.result}'",
            body: "Project: ${env.JOB_NAME}<br/>" +
                "Build Number: ${env.BUILD_NUMBER}<br/>" +
                "URL: ${env.BUILD_URL}<br/>",
            to: 'mahmoudhaleem100@gmail.com',
            attachmentsPattern: 'fs.html,image.html'
        }
    }
}
