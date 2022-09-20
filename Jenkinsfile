pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID="924034218347"
        AWS_DEFAULT_REGION="af-south-1" 
        CLUSTER_NAME="icasa-afs1-sit-cluster"
        SERVICE_NAME="icasa-afs1-sit-service"
        TASK_DEFINITION_NAME="icasa-afs1-sit-task"
        DESIRED_COUNT="2"
        IMAGE_REPO_NAME="icasa-afs1-sit-repo"
        IMAGE_TAG="${env.BUILD_ID}"
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
	registryCredential = "demo-admin-user"
    }
   
    stages {

    // Tests
    stage('Unit Tests') {
      steps{
        script {
          sh 'npm install'
	  sh 'npm test -- --watchAll=false'
        }
      }
    }
        
    // Building Docker images
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
        }
      }
    }
   
    // Uploading Docker images into AWS ECR
    stage('Pushing to ECR') {
     steps{  
         script {
			docker.withRegistry("https://" + REPOSITORY_URI, "ecr:${AWS_DEFAULT_REGION}:" + registryCredential) {
                    	dockerImage.push()
                	}
         }
        }
      }

    //  // Uploading Docker images into AWS ECR
    // stage('Gate Keeper Email For ApprovalR') {
    //  steps{  
    //      script {
		// 	docker.withRegistry("https://" + REPOSITORY_URI, "ecr:${AWS_DEFAULT_REGION}:" + registryCredential) {
    //                 	dockerImage.push()
    //             	}
    //      }
    //     }
    //   }

    // Deploying to Fargate AWS ECS     
    stage('Deploy') {
     steps{
            withAWS(credentials: registryCredential, region: "${AWS_DEFAULT_REGION}") {
                script {
			sh './script.sh'
                }
            } 
        }
      }      
      
    }
}

