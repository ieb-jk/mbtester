pipeline {

    agent any

    stages {

        stage('Checkout') {
            steps {
                echo 'Checkout source and do regualar stuff'
                echo "We are working with ${env.BRANCH_NAME}"
            }
        }

        stage('Merging') {
            steps {
                echo 'Develope branch merges Phinx migration scripts ran and Grunt applied'
                passed('CodeMerges')
            }
        }

        stage('UnitTesting') {
            steps {
                echo 'PhpUnit - contained code testing upto mock / stubbed php scripts'
                passed('UnitTesting')
            }
        }

        stage('ParallelTesting') {
            steps {
                parallel (
                    "StaticAnalysis" : { echo 'SonarPHP - Codesniffer, LinesOfCode, MessDetector, CopyPaste Detector, CodeBrowser, DOX' 
                        passed('StaticAnalysis') },
                    "Integration" : { echo 'BrowserStack with end to end testing'
                        failed('Integration') },
                    "LoadTesting" : { echo 'JMeter, Bench, Seige'
                        passed('LoadTesting') },
                    "Security" : { echo 'RIPs security scanning' 
                        passed('SecurityScan') }
                )
            }
        }

        stage('ReleaseToDark') {
            when {
                branch "PR-..*"
            }
            steps {
                passed('Deployment')
                echo "Deploy to 'dark' environment"
            }
        }
    }

}


void passed(context) { setBuildStatus ("ci/jenkins/${context}", "Passed!", 'SUCCESS') }

void failed(context) { setBuildStatus ("ci/jenkins/${context}", "Failed - see details", 'FAILURE') 
    slackNotification("bad","failure","@John.Kemp")
throw err}


void setBuildStatus(context, message, state) {
  step([
      $class: "GitHubCommitStatusSetter",
      contextSource: [$class: "ManuallyEnteredCommitContextSource", context: context],
      reposSource: [$class: "ManuallyEnteredRepositorySource", url: "https://github.com/ieb-jk/mbtester"],
      errorHandlers: [[$class: "ChangingBuildStatusErrorHandler", result: "UNSTABLE"]],
      statusResultSource: [ $class: "ConditionalStatusResultSource", results: [[$class: "AnyBuildResult", message: message, state: state]] ]
  ]);
}


void slackNotification(color, message, channel) {
     slackSend channel: channel, teamDomain: 'allbeauty', token: 'cOBOpfMoUQQpqxwkOXyy3vC8', color: color, message: message
}
