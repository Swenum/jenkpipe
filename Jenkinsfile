pipeline {
    agent any
    environment {
        NEW_BRANCH = "artefacts_branch"
    }
    stages {
        stage('Clone repository') {
            steps {
                    deleteDir()
                    git(
                                       url: 'git@github.com:Swenum/jenkpipe.git',
                                       credentialsId: 'Github_Repo_Swenum',
                                       branch: "12.simple"
                        )
            sh 'printenv'
            }
        }
        stage('Create Image') {
            steps {
                    sh "docker build -f nginx/Dockerfile -t test-nginx nginx/"
            }
        }
        stage('Git commit') {
            steps {

                sshagent(['Github_Repo_Swenum']) {
                    sh """
                                        git config --global push.default simple
                                        #docker image save test-nginx > docker_image_nginx.tar.gz
                                        #git add --all
                                        #git commit -m "Push artefact"
                                        #git  remote add  artefact git@github.com:Swenum/simplepythonapp.git
                                        #git push --set-upstream origin artefact
                                        #git push --set-upstream origin 12.simple
                                        docker tag  test-nginx swenum/nginx
                                        docker push swenum/nginx

                      """
                }


            }
        }
    }
    post {
        success {
            slackSend (color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
        }
        failure {
            slackSend (color: '#FF0000', message: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
        }
    }
}
