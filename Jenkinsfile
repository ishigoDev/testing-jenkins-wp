    pipeline {
        agent any

        environment {
            LOCAL_WP_CONTENT_DIR = 'wp-content' // The directory in your Git repo
            DOCKER_WP_CONTENT_DIR = '/var/www/html/wp-content' // The target path of the wp-content in your WordPress Docker container
            BRANCH_NAME = 'main' // Set your branch name
        }
        
        stages {
            stage('Check Branch') {
                steps {
                    script {
                        def gitBranch = sh(script: "git rev-parse --abbrev-ref HEAD", returnStdout: true).trim()
                        echo gitBranch
                        if (gitBranch != BRANCH_NAME) {
                            echo "Not on main branch. Skipping pipeline..."
                            currentBuild.result = 'ABORTED'
                            error("Stopping build since it's not on the main branch")
                        }
                        echo "Running pipeline on branch: ${gitBranch}"
                    }
                }
            }
            stage('Clone Repository') {
                steps {
                    script {
                        sh 'git reset --hard'
                        sh 'git pull origin main' // Fetch latest changes from main
                    }
                }
            }

            stage('Get Container Name') {
                steps {
                    script {
                        // Get container name dynamically
                        CONTAINER_NAME = sh(script: "docker ps --format '{{.Names}}' | grep wordpress", returnStdout: true).trim()
                        echo "Using container: ${CONTAINER_NAME}"
                    }
                }
            }

            stage('Copy wp-content to Container') {
                steps {
                    sh """
                    docker cp ${LOCAL_WP_CONTENT_DIR}/ ${CONTAINER_NAME}:${DOCKER_WP_CONTENT_DIR}
                    """
                }
            }

            stage('Verify Changes') {
                steps {
                    echo 'Deployment complete. Verify wp-content changes locally.'
                }
            }
        }
    }
