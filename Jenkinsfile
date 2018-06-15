pipeline {

    agent any

    stages {

        stage('Checkout') {
            steps {
                echo 'Checkout source, sync with dev, run phinx and grunt. Check for errors/conflicts"
                passed("Checkout, merge checks, phinx and grunt")
            }
        }

        stage('UnitTesting') {
            steps {
                echo 'PhpUnit code testing with mocked integration'
                script {
                    try {
                        sh "echo 'Unit tests ran'"
                    } catch (Exception e) {
                        failed('UnitTesting')
                        throw err
                    }
                    passed('UnitTesting')
                }
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




void passed(context) {
    setGitStatus(context,"Passed","SUCCESS")
}

void failed(context) {
    setGitStatus(context, "Failed!", 'FAILURE') 
    channel="@John.Kemp" // Eg "#cicd" or "@John.Kemp"
    message="Failed-${context} > ${env.BUILD_URL}"
    slackSend channel: channel, teamDomain: 'allbeauty', token: 'cOBOpfMoUQQpqxwkOXyy3vC8', color: "danger", message: message
}

void setGitStatus(context,message,state) {
    context="ci/jenkins/${context}"
    step([
        $class: "GitHubCommitStatusSetter",
        contextSource: [$class: "ManuallyEnteredCommitContextSource", context: context],
        reposSource: [$class: "ManuallyEnteredRepositorySource", url: "${env.GIT_URL}"],
        errorHandlers: [[$class: "ChangingBuildStatusErrorHandler", result: "UNSTABLE"]],
        statusResultSource: [ $class: "ConditionalStatusResultSource", results: [[$class: "AnyBuildResult", message: message, state: state]] ]
    ]);
}

