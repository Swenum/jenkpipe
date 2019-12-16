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
                    docker build -f nginx/Dockerfile -t swenum/test-nginx nginx/
                    docker push swenum/test-nginx
                    """
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
