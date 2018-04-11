pipeline {

    agent any

    stages {

        stage('Checkout') {
            steps {
                echo 'Checkout PR'
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
