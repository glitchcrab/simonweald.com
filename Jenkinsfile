def greenColor = "#00FF00"
def yellowColor = "#FFFF00"
def redColor = "#f44242"

def buildNotify(buildStatus) {
    // build status of null means successful
    buildStatus =  buildStatus ?: 'SUCCESSFUL'

    // Override default values based on build status
    if (buildStatus == 'STARTED') {
      colorCode = yellowColor
    } else if (buildStatus == 'SUCCESSFUL') {
      colorCode = greenColor
    } else {
      colorCode = redColor
    }   
    slackSend (color: colorCode, message: "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
}

node {
    def website

    currentBuild.result = "SUCCESS"

    try {
        stage('Start') {
            buildNotify 'STARTED'
        }
        stage('Clone repository') {
            checkout scm
            def shortCommit = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
        }
        stage ("Packer build") {
            environment {
                SHORT_COMMIT = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
            }
            sh 'packer build build.json'
        }
        stage ("Run tests with Goss") {
            environment {
                SHORT_COMMIT = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
            }
            sh 'dgoss run registry.analbeard.com/website/simonweald:$SHORT_COMMIT'
        }

    } catch (err) {
        currentBuild.result = "FAILED"
        throw err
    }  finally {
        buildNotify currentBuild.result
    }
}
