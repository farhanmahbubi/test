pipeline {
    agent any

    environment {
        IMAGE_NAME = 'test-app'
        CONTAINER_NAME = 'laravel_app'
        NGINX_CONTAINER = 'laravel_nginx'
        REPO_URL = 'https://github.com/farhanmahbubi/test.git'
        GIT_CREDENTIALS = 'github-pat'
        DOCKERHUB_CREDENTIALS = 'dockerhub-credentials'
        DOCKERHUB_REPO = 'farhannn/test-app'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: env.REPO_URL, branch: 'main', credentialsId: env.GIT_CREDENTIALS
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build(env.IMAGE_NAME)
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    docker.image(env.IMAGE_NAME).inside {
                        sh 'php artisan test --no-interaction --env=testing'
                    }
                }
            }
        }

        stage('Stop & Remove Old Container') {
            steps {
                script {
                    sh """
                    docker stop ${env.CONTAINER_NAME} || true
                    docker rm ${env.CONTAINER_NAME} || true
                    """
                }
            }
        }

        stage('Run New Container') {
            steps {
                script {
                    sh """
                    docker run -d --name ${env.CONTAINER_NAME} ${env.IMAGE_NAME}
                    """
                }
            }
        }

        stage('Reload Nginx') {
            steps {
                script {
                    sh "docker restart ${env.NGINX_CONTAINER}"
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('', env.DOCKERHUB_CREDENTIALS) {
                        docker.image(env.IMAGE_NAME).push('latest')
                    }
                }
            }
        }
    }

    post {
        always {
            echo "Pipeline selesai"
        }
        failure {
            echo "Pipeline gagal, cek log untuk perbaikan"
        }
    }
}
