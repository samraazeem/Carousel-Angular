pipeline{ 
    environment {
    registry = "samraazeem/angular-carousel"
    registryCredential = 'docker'
    dockerImage= ''
    }  
    agent any 
    tools{
        maven 'MAVEN'
        //npm 'NPM'
    }
    
    options {
        skipDefaultCheckout(true)
    }

    stages {
        stage("Code Checkout") {
            steps {
                git url: 'https://github.com/samraazeem/Carousel-Angular.git'
            }
        }
        stage('Build') {
            steps{
                sh 'npm install'
                sh 'npm run build'  
            }
        }
        stage('Unit Testing'){
            steps{
               sh 'npm run test -- --code-coverage'
            } 
        }
        stage('Sonar Analysis') {
			steps{
				withSonarQubeEnv('SONAR'){
					sh 'npm run sonar'
				}
			}
        }
		stage("Build Docker Image"){
            steps{
                sh 'docker build -t i-${username}-${BRANCH_NAME}:${BUILD_NUMBER} .'
            }
        }
        stage("Push Docker Image"){
            steps{
                sh "docker tag i-${username}-${BRANCH_NAME}:${BUILD_NUMBER} ${dockerUsername}/i-${username}-${BRANCH_NAME}:${BUILD_NUMBER}"
                sh "docker tag i-${username}-${BRANCH_NAME}:${BUILD_NUMBER} ${dockerUsername}/i-${username}-${BRANCH_NAME}"
                sh "docker push ${dockerUsername}/i-${username}-${BRANCH_NAME}:${BUILD_NUMBER}"
                sh "docker push ${dockerUsername}/i-${username}-${BRANCH_NAME}"
            }
        }
        stage('PreContainer Check'){
            steps{
                sh(script: "docker stop c-${username}-${BRANCH_NAME}", returnStatus: true)
                sh(script: "docker rm -f c-${username}-${BRANCH_NAME}", returnStatus: true)
            }
        }
        stage("Deployment"){
            parallel{
                stage('Docker Deployment'){
                    steps{
                        sh 'docker run --name c-${username}-${BRANCH_NAME} -d -p ${dockerPort}:80 -e branch=${BRANCH_NAME} i-${username}-${BRANCH_NAME}:${BUILD_NUMBER}'
                    }
                }
                stage('Kubernetes Deployment'){
                    steps{
                        sh 'kubectl config use-contexts Istio-Cluster'
                        sh 'kubectl apply -f ./kubernetes/frontend.yml -n=kubernetes-cluster-samraazeem'
                        sh 'kubectl apply -f ./kubernetes/backend.yml -n=kubernetes-cluster-samraazeem'
                    }
                }
            }
        }
    }
}

