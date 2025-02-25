def readmeFiles = []

pipeline {
    agent any

    stages {
        stage('Clean up') {
            steps {
                script {
                    cleanWs()
                }
            }
        }
        stage('Clone Repo') {
            steps {
                git url: 'https://github.com/adautomendes/md-to-confluence.git', branch: 'main', credentialsId: 'Github - adautomendes'
            }
        }
        stage('Find README.md files') {
            steps {
                script {
                    def filesFound = sh(script: 'find . -type f -name README.md', returnStdout: true).trim()
                    readmeFiles = filesFound.split('\n')
                    println "README.md files found:\n${readmeFiles}"
                }
            }
        }
        stage('Convert MD to Confluence') {
            steps {
                script {
                    readmeFiles.each { readme -> 
                        def fileContent = readFile(readme)
                        def firstLine = fileContent.readLines()[0]
                        println "First Line: ${firstLine}"

                        // Use regex to extract the numeric ID
                        def match = firstLine =~ /!PAGE-ID:\s*(\d+)/

                        if (match) {
                            def pageId = match[0][1] // Extract the captured group
                            println "Extracted PAGE-ID: ${pageId}"
                        } else {
                            println "No PAGE-ID found in the first line!"
                        }
                    }
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
            archiveArtifacts artifacts: '**/*.md', allowEmptyArchive: true
        }
    }
}
