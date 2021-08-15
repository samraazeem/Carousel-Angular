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
            when {
                branch 'development'
            }
            steps{
               sh 'npm run test:coverage'
            } 
        }
        stage('Sonar Analysis') {
            when {
                branch 'master'
            }
			steps{
				withSonarQubeEnv('SONAR'){
					sh 'sonar-scanner -Dsonar.projectVersion=${BUILD_NUMBER} -Dsonar.projectKey=test -Dsonar.host.url=http://52.249.217.108:9000 -Dsonar.login=b9b1f74acbea553a843f0af694b9aaeb78daa021'
				}
			}
		}
        /***stage('Docker Image') { 
            steps{
                
                sh 'docker build -t "samraazeem/angular-carousel":$BUILD_NUMBER"" .'
                script {
                    dockerImage= 'samraazeem/angular-carousel":$BUILD_NUMBER"'
                }
                script {
                    dockerImage= docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }
        stage('Container'){
            parallel {
                stage('PreContainer Check'){
                    steps{
                        sh 'docker rm -f angular-carousel'
                    }
                }
                stage('Publish DockerHub'){
                    steps{
                        script {
                            docker.withRegistry( '', registryCredential ) {
                            dockerImage.push()
                            }
                        }
                    }
                }
            }  
        }
        stage('Docker Deployment'){
          parallel {
                stage('Docker Deploy Development'){
                    steps{
                        sh 'docker rm -f angular-carousel'
                        sh 'docker run -d --name angular-carousel -p 7200:80 samraazeem/carousel-angular:"$BUILD_NUMBER"'
                    }
                }
               // stage('Docker Deploy Production'){
                    steps{
                        sh 'docker rm -f angular-carousel'
                        sh 'docker run -d --name angular-carousel -p 7300:80 samraazeem/angular-carousel:"$BUILD_NUMBER"'
                    }
               // }
            //}  
        } 
        stage('Kubernetes Deployment'){
            //parallel {
            //    stage('Docker Deploy Development'){
                    steps{
                      sh 'kubectl version'
                       // sh 'kubectl apply -f ./kubernetes/frontend.yaml'
                        //sh 'kubectl apply -f ./kubernetes/backend.yaml'
                    }
              //  }
                stage('Docker Deploy Production'){
                    steps{
                        sh 'docker run -d --name angular-carousel -p 7300:80 samraazeem/carousel-angular:"$BUILD_NUMBER"'
                    }
                }
            } 
        } ***/
    }
}
