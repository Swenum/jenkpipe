pipeline {
    agent any
    environment {
        NEW_BRANCH = "new_branch"
    }
    stages {
        stage('Clone repository') {
            steps {
                    deleteDir()
                    git(
                                       url: 'git@github.com:Swenum/test.git',
                                       credentialsId: 'Github_Repo_Swenum'

                        )
            }
        }
        stage('Create branch') {
            steps {
                    sh """
                        git checkout -b $NEW_BRANCH
                        git branch
                        git push origin $NEW_BRANCH
                    """
            }
        }
        stage('Delete branch') {
            steps {
                    sh 'git checkout master && git branch -D $NEW_BRANCH'
                    sh 'git push origin --delete $NEW_BRANCH'
            }
        }
        stage('Test') {
            steps {
                    sh 'echo "Remote branches are:"'
                    sh 'git branch -r'
                    sh 'echo "Local branches are:"'
                    sh 'git branch'
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