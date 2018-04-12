pipeline {

    agent any

    stages {

        stage('Checkout') {
            steps {
                echo 'Checkout source and do regualar stuff'
                echo "We are working with ${env.BRANCH_NAME}"
                setBuildStatus ('continuous-integration/jenkins/checkingout', 'Code checked out', 'SUCCESS')
            }
        }

        stage('Master') {
            when {
                branch 'master'
            }
            steps {
                echo 'Standard Master Routine'
            }
        }

        stage('PR-Routine') {
            when {
                branch 'PR-.*'
            }
            steps {
                echo 'Something specific to PRs'
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
