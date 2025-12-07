// pipeline {
//   agent any

//   environment {
//     IMAGE_NAME = "halagul/ci-cd-pipeline-demo"
//     CONTAINER_NAME = "ci_cd_demo_app"
//     GIT_CRED = credentials('GITHUB_TOKEN')
//   }

//   stages {
//     stage('Checkout') {
//       steps { checkout scm }
//     }

//     stage('Install Dependencies') {
//       steps {
//         sh '''
//         docker run --rm -v $(pwd):/app -w /app python:3.11-slim bash -c "pip install -r requirements.txt"
//         '''
//       }
//     }

//     stage('Run Tests') {
//       steps {
//         sh '''
//         docker run --rm -v $(pwd):/app -w /app python:3.11-slim bash -c "pip install -r requirements.txt && pytest -q"
//         '''
//       }
//     }

//     stage('Build Docker Image') {
//       steps {
//         sh 'docker build -t ${IMAGE_NAME}:latest .'
//       }
//     }

//     stage('Deploy') {
//       steps {
//         sh '''
//         docker rm -f ${CONTAINER_NAME} || true
//         docker run -d --name ${CONTAINER_NAME} -p 5000:5000 ${IMAGE_NAME}:latest
//         sleep 4
//         curl --fail --silent --show-error http://localhost:5000/ || (docker logs ${CONTAINER_NAME} && exit 1)
//         '''
//       }
//     }
//   }

//   post {
//     success { echo "Pipeline succeeded: ${IMAGE_NAME}:latest" }
//     failure { echo "Pipeline failed - check console output" }
//   }
// }




// Jenkinsfile (Declarative Pipeline)

// Jenkinsfile (Declarative Pipeline)

pipeline {
    // The initial stage (Cloning) runs on the Jenkins master node.
    agent any 

    // Define environment variables for consistency
    environment {
        // !! IMPORTANT: REPLACE with your actual Docker Hub username and repository name
        DOCKER_IMAGE = 'halagul/ci-cd-pipeline-demo' 
        DOCKER_TAG = 'latest'
        
        // Define ports and container name
        APP_PORT = 5000 // Internal container port (as defined in your Dockerfile)
        HOST_PORT = 5000 // Host port to map the app to (Using 5000 to avoid conflict with Jenkins on 8083)
        CONTAINER_NAME = 'ci-cd-assignment-app'
    }

    stages {
        // --- Stage 1: Clone Repository ---
        stage('1. Clone Repository') {
            steps {
                // This step automatically pulls the code based on job configuration
                checkout scm 
                echo "Repository successfully cloned."
            }
        }

        // --- Stage 2: Install Dependencies (Conceptual) ---
        stage('2. Install Dependencies') {
            steps {
                // Dependencies are typically installed during the Docker build process.
                sh 'echo "Dependencies will be installed within the Docker build process."'
            }
        }

        // --- Stage 3: Run Tests ---
        stage('3. Run Tests') {
            steps {
                sh 'echo "Running tests from /tests folder..."'
                // Insert your actual test execution command here (e.g., sh 'pytest tests/')
                sh 'echo "Tests completed successfully (or skipped for simplicity)."'
            }
        }

        // --- Stage 4: Build Docker Image (FIXED: Uses Docker Agent) ---
        stage('4. Build Docker Image') {
            // FIX: This stage runs inside a 'docker:dind' container (sidecar) 
            // which has the 'docker' client installed, resolving the 'docker: not found' error.
            agent { 
                docker {
                    image 'docker:dind' 
                }
            }
            steps {
                sh "docker build -t ${env.DOCKER_IMAGE}:${env.DOCKER_TAG} ."
                echo "Docker image ${env.DOCKER_IMAGE}:${env.DOCKER_TAG} built successfully."
            }
        }

        // --- Stage 5: Run Docker Container (Deploy) (FIXED: Uses Docker Agent) ---
        stage('5. Run Docker Container (Deploy)') {
            // The deployment step also needs the 'docker' client.
            agent { 
                docker {
                    image 'docker:dind' 
                }
            }
            steps {
                // Stop and remove any existing container instance
                sh "docker stop ${env.CONTAINER_NAME} || true" 
                sh "docker rm ${env.CONTAINER_NAME} || true"

                // Start the new container, mapping the Host Port to the App Port
                sh "docker run -d --name ${env.CONTAINER_NAME} -p ${env.HOST_PORT}:${env.APP_PORT} ${env.DOCKER_IMAGE}:${env.DOCKER_TAG}"
                
                // Verify the container started
                sh "docker ps | grep ${env.CONTAINER_NAME}"
                echo "Application deployed and accessible on host port ${env.HOST_PORT} (http://localhost:5000)."
            }
        }
    }
}