pipeline {
	agent any
	//agent { docker {image 'maven:3.6.3'}}
	//agent { docker {image 'node:13.8'}}

	environment {
		dockerHome = tool 'myDocker'
		mavenHome = tool 'myMaven'
		PATH = "$mavenHome/bin:$dockerHome/bin:$PATH"
	}

	stages {
		stage('Checkout') {
			steps{
				sh "mvn --version"
				sh "docker version"
				echo "Build"
				echo "BUILD_NUMBER - $env.BUILD_NUMBER"
				echo "BUILD_ID - $env.BUILD_ID"
				echo "BUILD_TAG - $env.BUILD_TAG"
				echo "BUILD_URL - $env.BUILD_URL"
				echo "JOB_NAME - $env.JOB_NAME"
			}
		}
		stage('Compile') {
			steps{
				sh "mvn clean compile"
			}
		}
		stage('Test') {
			steps{
				sh "mvn test"
			}
		}
		stage('Integration Test') {
			steps{
				sh "mvn failsafe:integration-test failsafe:verify"
			}
		}
		stage('Package') {
			steps{
				sh "mvn package -DskipTests"
			}
		}
		stage('Build Docker Image') {
			steps{
				// docker build -t nimalaali currency-exchange-devops:$env.BUILD_ID
				script {
					dockerImage = docker.build("nimalaali/currency-exchange-devops:${env.BUILD_ID}")
				}
			}
		}
		stage('Push Docker Image') {
			steps{
				script {
					docker.withRegistry('','Dockerhub'){
						dockerImage.push();
						dockerImage.push('latest');
					}					
				}
			}
		}
	}
	// post {
	// 	always {
	// 		echo "I run always"
	// 	}
	// 	success {
	// 		echo "I run only when you are successful"
	// 	}
	// 	failure {
	// 		echo "I run only when you fail"
	// 	}
	// }
 }
