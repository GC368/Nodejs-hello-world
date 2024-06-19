pipeline {
	agent any
	
	tools {
            nodejs 'Node16'
        }

	environment {
		// Define your backend environment variables
		AWS_CRED = 'aws_goexpert'
		AWS_DEFAULT_REGION = 'us-east-1'
		ECR_REPOSITORY = 'goexpert_ecr'
		ECS_CLUSTER_NAME = 'goexpert_cluster'
		ECS_SERVICE_NAME = 'goexpert-service'
		IMAGE_TAG = 'latest' // semver tag (sematic versioning) v1(major).3(minor).2(patch)
		AWS_ACCOUNT_ID = "637423296083"
		TASK_DEFINITION_FAMILY = "goexpert_task_def"
	}

	stages {
		// stage('Build Application') {
		// 	steps {
		// 		// Install dependencies and build the application
		// 		// sh 'npm install'
		// 		// sh 'npm run build'
		// 		sh 'docker-compose up -d'				
		// 	}
		// }

		stage('Build Docker Image') {
			steps {
				script {
					// Build the Docker image and tag it with ECR repoitory script
					 docker.build("${ECR_REPOSITORY}:${IMAGE_TAG}")
					// sh 'docker-compose up -d'
				}
				
			}
		}

		stage('Push Image to ECR') {
			steps {
				script {
					// Login to AWS ECR
					sh 'aws ecr get-login-password | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com'
					// Push Image to ECR
					// docker.push("${ECR_REPOSITORY}:${IMAGE_TAG}")
					// Tag Docker image
                    sh 'docker tag goexpert_ecr:latest ${AWS_ACCOUNT_ID}.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com/goexpert_ecr:latest'

                    // Push Docker image to ECR
                    // docker.image("$AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com/goexpert_ecr:latest").push()
					sh 'docker push 637423296083.dkr.ecr.us-east-1.amazonaws.com/goexpert_ecr:latest'
				    
				}				
			}
		}

		stage('Deploy to ECS') {
			steps {
				// Register task definition with the new image
				sh 'aws ecs register-task-definition file://ecs-container-definition.json'
				// Update the ECS service to use the new task definition
				sh 'aws ecs update-service --cluster ${ECS_CLUSTER_NAME} --service ${ECS_SERVICE_NAME} --task-definition ${TASK_DEFINITION_FAMILY}'
			}
		}
	}

	post {
		always {
			cleanWs()
		}
	}
}

