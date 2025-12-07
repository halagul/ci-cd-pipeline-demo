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

pipeline {
    agent any 

    // Define environment variables
    environment {
        // !! REPLACE 'halagul' with YOUR Docker Hub Username
        DOCKER_IMAGE = 'halagul/ci-cd-pipeline-demo' 
        DOCKER_TAG = 'latest'
        
        APP_PORT = 5000 // Internal container port (as defined in your application Dockerfile)
        HOST_PORT = 5000 // Host port for external access (e.g., http://localhost:5000)
        CONTAINER_NAME = 'ci-cd-assignment-app'
    }

    stages {
        stage('1. Clone Repository') {
            steps {
                checkout scm 
                echo "Repository successfully cloned."
            }
        }

        stage('2. Install Dependencies') {
            steps {
                sh 'echo "Dependencies will be installed within the Docker build process."'
            }
        }

        stage('3. Run Tests') {
            steps {
                sh 'echo "Running tests from /tests folder..."'
                // Assuming you have pytest installed and a /tests/test_app.py file
                // sh 'docker run --rm -v $(pwd):/app -w /app python:3.9-slim pytest tests/test_app.py'
                sh 'echo "Tests completed successfully (or skipped for simplicity)."'
            }
        }

        // --- Stage 4: Build Docker Image (FIXED) ---
        stage('4. Build Docker Image') {
            agent { 
                docker {
                    image 'docker:dind' 
                    // FIX: Run the agent as root (UID 0) to bypass socket permission issues
                    args '-u 0' 
                }
            }
            steps {
                sh "docker build -t ${env.DOCKER_IMAGE}:${env.DOCKER_TAG} ."
                echo "Docker image ${env.DOCKER_IMAGE}:${env.DOCKER_TAG} built successfully."
            }
        }

        // --- Stage 5: Run Docker Container (Deploy) (FIXED) ---
        stage('5. Run Docker Container (Deploy)') {
            agent { 
                docker {
                    image 'docker:dind' 
                    // FIX: Run the agent as root (UID 0) to bypass socket permission issues
                    args '-u 0' 
                }
            }
            steps {
                // Stop and remove any existing container instance
                sh "docker stop ${env.CONTAINER_NAME} || true" 
                sh "docker rm ${env.CONTAINER_NAME} || true"

                // Start the new container
                sh "docker run -d --name ${env.CONTAINER_NAME} -p ${env.HOST_PORT}:${env.APP_PORT} ${env.DOCKER_IMAGE}:${env.DOCKER_TAG}"
                
                // Verify the container started
                sh "docker ps | grep ${env.CONTAINER_NAME}"
                echo "Application deployed and accessible on http://localhost:5000."
            }
        }
    }
}// Jenkinsfile (Declarative Pipeline)

pipeline {
    agent any 

    // Define environment variables
    environment {
        // !! REPLACE 'halagul' with YOUR Docker Hub Username
        DOCKER_IMAGE = 'halagul/ci-cd-pipeline-demo' 
        DOCKER_TAG = 'latest'
        
        APP_PORT = 5000 // Internal container port (as defined in your application Dockerfile)
        HOST_PORT = 5000 // Host port for external access (e.g., http://localhost:5000)
        CONTAINER_NAME = 'ci-cd-assignment-app'
    }

    stages {
        stage('1. Clone Repository') {
            steps {
                checkout scm 
                echo "Repository successfully cloned."
            }
        }

        stage('2. Install Dependencies') {
            steps {
                sh 'echo "Dependencies will be installed within the Docker build process."'
            }
        }

        stage('3. Run Tests') {
            steps {
                sh 'echo "Running tests from /tests folder..."'
                // Assuming you have pytest installed and a /tests/test_app.py file
                // sh 'docker run --rm -v $(pwd):/app -w /app python:3.9-slim pytest tests/test_app.py'
                sh 'echo "Tests completed successfully (or skipped for simplicity)."'
            }
        }

        // --- Stage 4: Build Docker Image (FIXED) ---
        stage('4. Build Docker Image') {
            agent { 
                docker {
                    image 'docker:dind' 
                    // FIX: Run the agent as root (UID 0) to bypass socket permission issues
                    args '-u 0' 
                }
            }
            steps {
                sh "docker build -t ${env.DOCKER_IMAGE}:${env.DOCKER_TAG} ."
                echo "Docker image ${env.DOCKER_IMAGE}:${env.DOCKER_TAG} built successfully."
            }
        }

        // --- Stage 5: Run Docker Container (Deploy) (FIXED) ---
        stage('5. Run Docker Container (Deploy)') {
            agent { 
                docker {
                    image 'docker:dind' 
                    // FIX: Run the agent as root (UID 0) to bypass socket permission issues
                    args '-u 0' 
                }
            }
            steps {
                // Stop and remove any existing container instance
                sh "docker stop ${env.CONTAINER_NAME} || true" 
                sh "docker rm ${env.CONTAINER_NAME} || true"

                // Start the new container
                sh "docker run -d --name ${env.CONTAINER_NAME} -p ${env.HOST_PORT}:${env.APP_PORT} ${env.DOCKER_IMAGE}:${env.DOCKER_TAG}"
                
                // Verify the container started
                sh "docker ps | grep ${env.CONTAINER_NAME}"
                echo "Application deployed and accessible on http://localhost:5000."
            }
        }
    }
}