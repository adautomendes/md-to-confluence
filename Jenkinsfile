pipeline {
    pipeline {
    agent any

    stages {
        stage('Clone Repo') {
            steps {
                git url: 'https://github.com/adautomendes/md-to-confluence.git', branch: 'main'
            }
        }
        stage('Find README.md files') {
            steps {
                script {
                    def readmeFiles = sh(script: 'find . -type f -name README.md', returnStdout: true).trim()
                    println "README.md files found:\n${readmeFiles}"
                }
            }
        }
    }
    post {
        failure {
            echo 'The pipeline failed.'
        }
        success {
            echo 'Pipeline concluded successfully!'
            archiveArtifacts artifacts: '**/*.*', allowEmptyArchive: true
        }
    }
}
