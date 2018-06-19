pipeline {

    environment {
        SLACK = credentials("Slack")    
    }
    
    agent any

    stages {

        stage('Checkout') {
            steps {
                echo 'Checkout source, report conflicts and issues with migrations or grunt'
                passed('BasicChecks')
            }
        }

        stage('UnitTesting') {
            steps {
                echo 'PhpUnit - component testing with mock api stubs'
                sh "echo 'Unit tests are running'"
            }
        }

        stage('ParallelTesting') {
            steps {
                parallel (
                    "StaticAnalysis" : { echo 'SonarPHP - Codesniffer, LinesOfCode, MessDetector, CopyPaste Detector, CodeBrowser, DOX' 
                        passed('StaticAnalysis') },
                    "Integration" : { echo 'BrowserStack with end to end testing'
                        passed('IntegrationTesting') },
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
    slackNotification("danger","${context}-failed > ${env.BO_URL}","#John.Kemp")
}


void setBuildStatus(context, message, state) {
  step([
      $class: "GitHubCommitStatusSetter",
      contextSource: [$class: "ManuallyEnteredCommitContextSource", context: context],
      reposSource: [$class: "ManuallyEnteredRepositorySource", url: "${env.GIT_URL}"],
      errorHandlers: [[$class: "ChangingBuildStatusErrorHandler", result: "UNSTABLE"]],
      statusResultSource: [ $class: "ConditionalStatusResultSource", results: [[$class: "AnyBuildResult", message: message, state: state]] ]
  ]);
}


void slackNotification(color, message, channel) {
     slackSend channel: channel, teamDomain: 'allbeauty', token: "${env.SLACK}", color: color, message: message
}