node {

    stage('Checkout') {
        echo 'Checkout source and do regualar stuff'
        echo "We are working with ${env.BRANCH_NAME}"
        sh "env"
    }

    stage('Merging') {
        echo 'Develope branch merges Phinx migration scripts ran and Grunt applied'
        passed('CodeMerges')
    }

    stage('UnitTesting') {
        echo 'PhpUnit - contained code testing upto mock / stubbed php scripts'
        passed('UnitTesting')
    }

    stage("StaticAnalysis") {
        echo 'SonarPHP - Codesniffer, LinesOfCode, MessDetector, CopyPaste Detector, CodeBrowser, DOX'
        passed('StaticAnalysis') 
    }

    stage("Integration") {
        echo 'BrowserStack with end to end testing'
        passed('IntegrationTesting')
    }
}



void passed(context) {
    setGitStatus(context,"Passed!","SUCCESS")
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
