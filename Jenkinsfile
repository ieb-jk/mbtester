pipeline {
    
    agent any

    stages {

        stage('Checkout') {
            steps {
                echo 'Checkout source, report conflicts and issues with migrations or grunt'
            }
        }

        stage('UnitTesting') {
            steps {
                echo 'PhpUnit - component testing with mock api stubs'
                sh "echo 'Unit tests are running with success'"
            }
        }

        stage('ParallelTesting') {
            steps {
                parallel (
                    "StaticAnalysis" : { echo 'SonarPHP - Codesniffer, LinesOfCode, MessDetector, CopyPaste Detector, CodeBrowser, DOX' },
                    "Integration" : { echo 'BrowserStack with end to end testing' },
                    "LoadTesting" : { echo 'JMeter, Bench, Seige' },
                    "Security" : { echo 'RIPs security scanning' }
                )
            }
        }

        stage('ReleaseToDark') {
            when {
                branch "PR-..*"
            }
            steps {
                echo "Deploy to 'dark' environment"
            }
        }
    }

}
