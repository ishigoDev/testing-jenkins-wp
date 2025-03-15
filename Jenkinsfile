pipeline {
    agent any

    environment {
        LOCAL_WP_CONTENT_DIR = 'wp-content' // The directory in your Git repo
        DOCKER_WP_CONTENT_DIR = '/var/www/html/wp-content' // The target path of the wp-content in your WordPress Docker container
        CONTAINER_NAME = 'setup-wp-using-docker_wordpress_1' // Name of your WordPress container
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/ishigoDev/testing-jenkins-wp.git'
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
