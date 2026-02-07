pipeline {
    agent any

    environment {
        JMETER_HOME = "D:\\JMeter\\apache-jmeter-5.6.3\\bin"
        TEST_PLAN   = "jmeter\\testplans\\orangehrm_login_load_test.jmx"
        RESULT_FILE = "jmeter\\results\\result.jtl"
        REPORT_DIR  = "jmeter\\report"
    }

    stages {

        stage("Checkout") {
            steps {
                checkout scm
            }
        }

        stage("Run JMeter Load Test") {
            steps {
                echo "🚀 Running OrangeHRM Login Load Test..."

                bat """
                echo ==========================================
                echo Cleaning old results...
                echo ==========================================

                if not exist jmeter\\results mkdir jmeter\\results

                REM JMeter requires report folder to be empty
                if exist %REPORT_DIR% rmdir /s /q %REPORT_DIR%

                echo ==========================================
                echo Executing JMeter Test Plan...
                echo ==========================================

                "%JMETER_HOME%\\jmeter.bat" -n ^
                  -t "%TEST_PLAN%" ^
                  -l "%RESULT_FILE%" ^
                  -e -o "%REPORT_DIR%"
                """
            }
        }

        stage("Publish HTML Report") {
            steps {
                echo "📊 Publishing JMeter HTML Report..."

                publishHTML([
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: 'jmeter/report',
                    reportFiles: 'index.html',
                    reportName: 'OrangeHRM Performance Test Report'
                ])
            }
        }
    }

    post {
        always {
            echo "📦 Archiving test artifacts..."

            archiveArtifacts artifacts: 'jmeter/results/*.jtl', fingerprint: true
            archiveArtifacts artifacts: 'jmeter/report/**', fingerprint: false
        }

        success {
            echo "✅ Performance Test Completed Successfully!"
        }

        failure {
            echo "❌ Performance Test Failed! Check Jenkins console output."
        }
    }
}
