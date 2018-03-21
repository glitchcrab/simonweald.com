node {
    def website

    currentBuild.result = "SUCCESS"

    stage('Start') {
        slackSend (color: '#FFFF00', message: "STARTED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
    }

    try {

        stage('Clone repository') {
            checkout scm
            shortCommit = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
        }

        stage ("Packer build") {
            sh 'packer build build.json'
        }

        stage ("Run tests with Goss") {
            sh 'dgoss run registry.analbeard.com/website/simonweald:$commit'
        }

        # if tests succeed, then push to registry

        stage('End') {
            slackSend (color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
        }

    }

    catch (err) {

        currentBuild.result = "FAILURE"

            slackSend (color: '#f44242', message: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")

        throw err

    }

}
