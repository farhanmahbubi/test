pipeline {
    agent any

    environment {
        IMAGE_NAME = 'test-app'            // Nama image Laravel kamu
        CONTAINER_NAME = 'laravel_app'    // Nama container Laravel app
        NGINX_CONTAINER = 'laravel_nginx' // Nama container nginx
        REPO_URL = 'https://github.com/farhanmahbubi/repo-test.git' // Ganti dengan repo kamu
        DOCKERHUB_CREDENTIALS = 'dockerhub-credentials' // Jika push ke Docker Hub
        DOCKERHUB_REPO = 'farhannn/test-app'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: env.REPO_URL, branch: 'main' // Sesuaikan branch
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
                    docker run -d --name ${env.CONTAINER_NAME} -p 9000:9000 ${env.IMAGE_NAME}
                    """
                }
            }
        }

        stage('Reload Nginx') {
            steps {
                script {
                    sh """
                    docker restart ${env.NGINX_CONTAINER}
                    """
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
