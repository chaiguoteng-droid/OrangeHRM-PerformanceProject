pipeline {
    agent any

    environment {
        // IMPORTANT: Always wrap Windows paths with quotes
        JMETER_BIN = "D:\\JMeter\\apache-jmeter-5.6.3\\bin\\jmeter.bat"
    }

    stages {

        stage("Checkout") {
            steps {
                checkout scm
            }
        }

        stage("Run JMeter Load Test") {
            steps {
                bat """
                echo ==========================================
                echo Running OrangeHRM Login Load Test...
                echo ==========================================

                REM Create results folder if not exists
                if not exist "jmeter\\results" mkdir "jmeter\\results"

                REM Delete old report folder if exists
                if exist "jmeter\\report" rmdir /s /q "jmeter\\report"

                REM Run JMeter in Non-GUI mode
                "%JMETER_BIN%" -n ^
                  -t "jmeter\\testplans\\orangehrm_login_load_test.jmx" ^
                  -l "jmeter\\results\\result.jtl" ^
                  -e -o "jmeter\\report"

                echo ==========================================
                echo JMeter Test Completed!
                echo ==========================================
                """
            }
        }

        stage("Archive Test Results") {
            steps {
                archiveArtifacts artifacts: 'jmeter/results/*.jtl', fingerprint: true
            }
        }

        stage("Publish HTML Report") {
            steps {
                publishHTML([
                    reportDir: 'jmeter/report',
                    reportFiles: 'index.html',
                    reportName: 'OrangeHRM Performance Test Report',
                    keepAll: true,
                    alwaysLinkToLastBuild: true,
                    allowMissing: false
                ])
            }
        }
    }

    post {
        success {
            echo "✅ Performance Test Pipeline Finished Successfully!"
        }
        failure {
            echo "❌ Pipeline Failed. Please check Console Output."
        }
    }
}


