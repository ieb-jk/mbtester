pipeline {

    agent any

    stages {

        stage('Checkout') {
            steps {
                echo 'Checkout source'
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
                branch 'PR-..*'
            }
            steps {
                echo 'Something specific to PRs'
            }
        }


    }
}
