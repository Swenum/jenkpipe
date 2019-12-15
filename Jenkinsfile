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
            }
        }
        stage('Create Image') {
            steps {
                    sh "docker build -f nginx/Dockerfile -t $BUILD_TAG-nginx nginx/"
            }
        }
        stage('Git commit') {
            steps {
                sh """
                    git add --all
                    git commit -m "Push artefact"
                    git push origin
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
