pipeline {
    agent any
    stages {
        stage('Clone repository') {
            steps {
            git(
                   url: 'git@github.com:Swenum/sa.it-academy.by.git',
                   credentialsId: 'Github_Repo_Swenum',
                   branch: "master"
                )

            }
        }
        stage('Checking repository'){
            steps {
                sh "ls -l"
            }
        }
        stage('Packing project') {
            steps {
                sh """
                tar -zcvf /tmp/package.tar.gz  ./
                """
                deleteDir()
                sh "mv /tmp/package.tar.gz  ./"
            }
        }
        stage('Packing test') {
            steps {
                sh "ls -l"
            }

       
        }
    }
}