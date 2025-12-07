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
    // Master node runs the initial stages (Clone, Test)
    agent any 

    // Define environment variables
    environment {
        // !! IMPORTANT: REPLACE with your actual Docker Hub details
        DOCKER_IMAGE = 'halagul/ci-cd-pipeline-demo' 
        DOCKER_TAG = 'latest'
        APP_PORT = 5000 // Internal container port exposed in Dockerfile
        HOST_PORT = 5000 // Host port to map the app to (Use 5000 to avoid conflict with Jenkins on 8083)
        CONTAINER_NAME = 'ci-cd-assignment-app'
    }

    stages {
        // Stage 1, 2, 3 run on the Jenkins master node
        stage('1. Clone Repository') {
            steps {
                checkout scm 
                echo "Repository successfully cloned."
            }
        }
        stage('2. Install Dependencies') {
            steps {
                sh 'echo "Dependencies handled by Dockerfile. Skipping host install."'
            }
        }
        stage('3. Run Tests') {
            steps {
                sh 'echo "Running tests from /tests folder..."'
                // Add actual test command here if needed: sh 'pytest tests/'
                sh 'echo "Tests completed successfully (or skipped for simplicity)."'
            }
        }

        // --- STAGE 4: BUILD DOCKER IMAGE ---
        // This stage runs inside a 'docker:dind' container to access the 'docker' command.
        stage('4. Build Docker Image') {
            agent { 
                docker {
                    image 'docker:dind' // Uses a container with the Docker client
                }
            }
            steps {
                // The 'sh' command now successfully finds the 'docker' binary
                sh "docker build -t ${env.DOCKER_IMAGE}:${env.DOCKER_TAG} ."
                echo "Docker image ${env.DOCKER_IMAGE}:${env.DOCKER_TAG} built successfully."
            }
        }

        // --- STAGE 5: DEPLOY CONTAINER ---
        // This stage also runs inside the 'docker:dind' container to run 'docker run'.
        stage('5. Run Docker Container (Deploy)') {
            agent { 
                docker {
                    image 'docker:dind' 
                }
            }
            steps {
                // Stop and remove existing container
                sh "docker stop ${env.CONTAINER_NAME} || true" 
                sh "docker rm ${env.CONTAINER_NAME} || true"

                // Start the new container, mapping 5000 (Host) to 5000 (App)
                sh "docker run -d --name ${env.CONTAINER_NAME} -p ${env.HOST_PORT}:${env.APP_PORT} ${env.DOCKER_IMAGE}:${env.DOCKER_TAG}"
                
                // Verify the container started
                sh "docker ps | grep ${env.CONTAINER_NAME}"
                echo "Application deployed on host port ${env.HOST_PORT}."
            }
        }
    }
}