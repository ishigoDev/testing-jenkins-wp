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
                        sh 'git fetch --all'
                        sh 'git checkout main' // Ensure we're on the main branch
                        def gitBranch = sh(script: "git rev-parse --abbrev-ref HEAD", returnStdout: true).trim()
                        echo "Current branch: ${gitBranch}"
                        if (gitBranch != BRANCH_NAME) {
                            echo "Not on main branch. Skipping pipeline..."
                            currentBuild.result = 'ABORTED'
                            error("Stopping build since it's not on the main branch")
                        }
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
                        CONTAINER_NAME = sh(script: "docker ps --filter ancestor=wordpress --format '{{.Names}}'", returnStdout: true).trim()
                        echo "Using container: ${CONTAINER_NAME}"
                    }
                }
            }

            // stage('Copy wp-content to Container') {
            //     steps {
            //         sh """
            //         docker cp ${LOCAL_WP_CONTENT_DIR}/ ${CONTAINER_NAME}:${DOCKER_WP_CONTENT_DIR}
            //         """
            //     }
            // }
            stage('Copy Changed Files to Container') {
                steps {
                    script {
                        def changedFiles = sh(script: "git diff --name-only HEAD~1 HEAD ${LOCAL_WP_CONTENT_DIR}", returnStdout: true).trim()
                        if (changedFiles) {
                            echo "Changed files:\n${changedFiles}"
                            changedFiles.split('\n').each { file ->
                                sh "docker cp ${file} ${CONTAINER_NAME}:${DOCKER_WP_CONTENT_DIR}/${file.replaceFirst('^' + Pattern.quote(LOCAL_WP_CONTENT_DIR) + '/', '')}"
                            }
                        } else {
                            echo "No changes detected in ${LOCAL_WP_CONTENT_DIR}."
                        }
                    }
                }
            }

            stage('Verify Changes') {
                steps {
                    echo 'Deployment complete. Verify wp-content changes locally.'
                }
            }
        }
        post {              
              success {
        script {
            echo env.BUILD_URL
            echo env.GIT_COMMIT
            step([
    $class: 'GitHubCommitStatusSetter',
    reposSource: [$class: 'ManuallyEnteredRepositorySource', url: 'https://github.com/ishigoDev/testing-jenkins-wp.git'],
    commitShaSource: [$class: 'ManuallyEnteredShaSource',  sha: "${env.GIT_COMMIT}"],
    context: 'Jenkins Build',
    description: 'Build completed',
    state: 'SUCCESS',
    statusBackref: env.BUILD_URL,
])
            
        }
        echo "✅ Build was successful! Build URL: ${env.BUILD_URL}"
    }   
        }
    }
