pipeline {
	agent any
	environment {
		IMAGE_NAME = "adobe-webapp"
		CONTAINER_NAME = "adobe-container"
		PROD_SERVER = "ec2-user@172.31.69.196"
	}
	stages {
		stage('Checkout') {
			steps {
				git branch: "${env.BRSNCH_NAME}", url: 'git@github.com:aj1232/Devops_Project_1.git'
			}
		}
		stage('Build Docker'){
			steps {
				sh 'docker build -t $IMAGE_NAME .'
			}
		}
		stage('Test'){
			steps{
				sh '''
				docker run -d --name test-container $IMAGE_NAME
				sleep 5
				docker ps
				docker rm -f test-container
				'''
			}
		}
		stage('Deploy'){
			when{ branch 'master' }
			steps {
				sh '''
				ssh $PROD_SERVER "docker stop $CONTAINER_NAME || true"
				ssh $PROD_SERVER "docker rm $CONTAINER_NAME || true"
				docker save $IMAGE_NAME | ssh $PROD_SERVER docker load
				ssh $PROD_SERVER "docker run -d -p 80:80 --name $CONTAINER_NAME $IMAGE_NAME"
				'''
			}
		}
	}
	post {
		always { sh 'docker rm -f test-container || true' }
	}
}
