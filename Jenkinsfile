pipeline {
    agent any
    
    tools {
        nodejs 'node16'
    }
    environment{
        SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {
        stage('clean workspace') {
            steps {
                cleanWs()
            }
        }
        
         stage('Checkout Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/haleem00/Netflix-clone.git'
            }
        }
        
        //  stage('Sonar analysis') {
        //     steps {
        //         withSonarQubeEnv('sonar') {
        //             sh ''' 
        //               $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Netflix \
        //               -Dsonar.projectKey=Netflix
        //             '''
        //       }
        //     }
        // }
        
        //   stage("quality gate"){
        //    steps {
        //         script {
        //             waitForQualityGate abortPipeline: false, credentialsId: 'Sonar-token'
        //         }
        //     }
        // }
        
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
        
        // stage('OWASP FS SCAN') {
        //     steps {
        //         dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'Dependency-Check'
        //         dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
        //     }
        // }
        
        stage('trivy fs scan') {
            steps {
                sh 'trivy fs --format table --output fs.html .'
                }
            }
        
        stage('Docker image build') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker_pass') {
                        sh 'docker build --build-arg TMDB_V3_API_KEY=b16d8eed9624150739d555b64b2a569c -t haleemo/netflix-clone:latest .' 
                    }
                }
            }
        }
        
        stage('trivy image scan') {
            steps {
                sh 'trivy image --format table --output image.html haleemo/netflix-clone:latest'
                }
            }

        stage('Docker image push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker_pass') {
                        sh 'docker push haleemo/netflix-clone:latest' 
                    }
                }
            }
        }
        
            
        // stage('K8') {
        //     steps {
        //         dir('Kubernetes') { // Specify the directory here
        //             withKubeCredentials(kubectlCredentials: [[
        //             caCertificate: '', 
        //             clusterName: 'EKS_CLOUD', 
        //             contextName: '', 
        //             credentialsId: 'k8-token', 
        //             namespace: 'webapps', 
        //             serverUrl: 'https://2694E103F67E27D4115E703BA534F8D5.gr7.us-east-1.eks.amazonaws.com'
        //             ]]) {
        //                 sh 'kubectl apply -f deployment.yml'
        //                 sh 'kubectl apply -f service.yml'
        //                 }
        //         }
        //     }
        // }

            
            
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
