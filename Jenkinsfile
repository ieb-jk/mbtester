pipeline {

    agent any

    stages {

        stage('Checkout') {
            steps {
                echo 'Checkout source and do regualar stuff'
                echo "We are working with ${env.BRANCH_NAME}"
                setBuildStatus ('ci/jenkins/checkout', 'Code checked out', 'SUCCESS')
            }
        }

        stage('UnitTesting') {
            steps {
                echo 'PhpUnit - contained code testing upto mock / stubbed php scripts. test Migration scripts and scan for errors'
                setBuildStatus ('ci/jenkins/unit-testing', 'Passed!', 'SUCCESS')
            }
        }

        stage('ParallelTesting') {
            steps {
                parallel (
                    "StaticAnalysis" : { echo 'SonarPHP - Codesniffer, LinesOfCode, MessDetector, CopyPaste Detector, CodeBrowser, DOX' 
                            setBuildStatus ('ci/jenkins/parallel/StaticAnalysis', 'Passed!', 'SUCCESS')
                        },
                    "Integration" : { echo 'BrowserStack with end to end testing' },
                    "LoadTesting" : { echo 'JMeter, Bench, Seige' },
                    "Security" : { echo 'RIPs security scanning' }
                )
            }
        }

        stage('PromoteToDark') {
            steps {
                echo 'Initially deployed to staged environment for final approval, before switching live/dark urls'
                setBuildStatus ('ci/jenkins/deployment', 'Passed!', 'SUCCESS')
            }
        }
    }

}


void setBuildStatus(context, message, state) {
  step([
      $class: "GitHubCommitStatusSetter",
      contextSource: [$class: "ManuallyEnteredCommitContextSource", context: context],
      reposSource: [$class: "ManuallyEnteredRepositorySource", url: "https://github.com/ieb-jk/mbtester"],
      errorHandlers: [[$class: "ChangingBuildStatusErrorHandler", result: "UNSTABLE"]],
      statusResultSource: [ $class: "ConditionalStatusResultSource", results: [[$class: "AnyBuildResult", message: message, state: state]] ]
  ]);
}
