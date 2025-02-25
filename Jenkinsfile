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
                        def dir = readme.lastIndexOf("/") >= 0 ? readme[0..readme.lastIndexOf("/") - 1] : ""
                        def fileName = readme.split("/")[-1]

                        def stdOut = sh(script: """
                            pandoc --from=markdown --to=jira --output=${dir}/${fileName}.wiki ${readme}
                        """, returnStdout: true).trim()

                        println "${stdOut}"
                    }
                }
            }
        }
        stage('Post updated doc to Confluence') {
            steps {
                script {
                    readmeFiles.each { readme ->
                        def pageId = extractPageId(readme)
                        println "Extracted PAGE-ID: ${pageId}"


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
            archiveArtifacts artifacts: '**/*.wiki', allowEmptyArchive: true
        }
    }
}

def extractPageId(readme) {
    def fileContent = readFile(readme)
    def firstLine = fileContent.readLines()[0]

    // Use regex to extract the numeric ID
    def match = firstLine =~ /<!-- PAGE-ID:\s*(\d+) -->/

    if (match) {
        return match[0][1]
    }

    println "No PAGE-ID found in the first line!"
    return
}