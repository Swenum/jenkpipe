pipeline {
    agent any
    environment {
        REPO = 'swenum/wordpress'

    }
         stages {
                stage('Clone repository') {
                    steps {
                       script {
                                COMMIT = "${GIT_COMMIT.substring(0,8)}"
                       }
                       deleteDir()
                       git(
                                 url: 'git@github.com:Swenum/jenkpipe',
                                 credentialsId: 'Github_Repo_Swenum',
                                 branch: "12.Homework"
                        )
                    sh 'printenv'
                    sh 'print ${COMMIT}'
                    }
                }

        stage ('Docker build Nginx Php-Fpm') {
            parallel {
                stage ('Wodpress Nginx'){
                    agent { label 'docker'}
                    steps {
                        sh "docker build -f nginx/Dockerfile -t ${REPO}:${COMMIT}-nginx nginx/"
                    }
                    post {
                        success {
                            echo 'Tag for private registry'
                            sh "docker tag ${REPO}:${COMMIT}-nginx"
                        }
                    }
                }
                stage ('Wordpress PHP-FPM') {
                    agent { label 'docker'}
                    steps {
                        sh "docker build -f php7-fpm/Dockerfile -t ${REPO}:${COMMIT}-fpm php7-fpm/"
                    }
                    post {
                        success {
                            echo 'Tag for private registry'
                            sh "docker tag ${REPO}:${COMMIT}-fpm"
                        }
                    }
                }
                stage ('Wordpress CLI') {
                    agent { label 'docker'}
                    steps {
                        sh "docker build -f cli/Dockerfile -t ${REPO}:${COMMIT}-cli cli/"
                    }
                    post {
                        success {
                            echo 'Tag for private registry'
                            sh "docker tag ${REPO}:${COMMIT}-cli"
                        }
                    }
                }
            }
        }
        stage ('Run'){
            parallel {
                stage ('Micro-Services'){
                    agent { label 'docker'}
                    steps {
                        // Create Network
                        sh "docker network create wordpress-micro-${BUILD_NUMBER}"
                        // Start database
                        sh "docker run -d --name 'mariadb-${BUILD_NUMBER}' -e MYSQL_ROOT_PASSWORD=wordpress -e MYSQL_USER=wordpress -e MYSQL_PASSWORD=wordpress -e MYSQL_DATABASE=wordpress --network wordpress-micro-${BUILD_NUMBER} amd64/mariadb:10.0"
                        sleep 15
                        // Start Memcached
                        sh "docker run -d --name 'memcached-${BUILD_NUMBER}' --network wordpress-micro-${BUILD_NUMBER} memcached"
                        // Start application micro-services
                        sh "docker run -d --name 'fpm-${BUILD_NUMBER}' --link mariadb-${BUILD_NUMBER}:mariadb --link memcached-${BUILD_NUMBER}:memcached --network wordpress-micro-${BUILD_NUMBER} -v wordpress-micro-data:/var/www/html ${REPO}:${COMMIT}-fpm"
                        sh "docker run -d --name 'nginx-${BUILD_NUMBER}' --link fpm-${BUILD_NUMBER}:wordpress --link memcached-${BUILD_NUMBER}:memcached --network wordpress-micro-${BUILD_NUMBER} -v wordpress-micro-data:/var/www/html ${REPO}:${COMMIT}-nginx"
                        // Get container IDs
                        script {
                            DOCKER_FPM   = sh(script: "docker ps -qa -f ancestor=${REPO}:${COMMIT}-fpm", returnStdout: true).trim()
                            DOCKER_NGINX = sh(script: "docker ps -qa -f ancestor=${REPO}:${COMMIT}-nginx", returnStdout: true).trim()
                        }
                    }
                }
            }
        }

        }
    }

