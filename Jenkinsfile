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
        stage('Build Image') {
            steps {
                    sh """
                    docker build -f nginx/Dockerfile -t test-nginx nginx/

                    """            }
        }
        stage('Git commit') {
            steps {

                sshagent(['Github_Repo_Swenum']) {
                    sh """
                                        git config --global push.default simple
                                        docker image inspect  test-nginx > inspect_image.txt
                                        git add --all
                                        git commit -m "Add Artefact"
                                        git push --set-upstream origin 12.simple

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
