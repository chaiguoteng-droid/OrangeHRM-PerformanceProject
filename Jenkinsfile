pipeline {
    agent any

    environment {
        JMETER_BIN = "D:\\JMeter\\apache-jmeter-5.6.3\\bin\\jmeter.bat"
        JAVA21_HOME = "C:\\Program Files\\Java\\jdk-21"
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
                echo Running OrangeHRM Login Load Test...

                set JAVA_HOME=%JAVA21_HOME%
                set PATH=%JAVA_HOME%\\bin;%PATH%

                java -version

                if not exist "jmeter\\results" mkdir "jmeter\\results"
                if exist "jmeter\\report" rmdir /s /q "jmeter\\report"

                "%JMETER_BIN%" -n ^
                  -t "jmeter\\testplans\\orangehrm_login_load_test.jmx" ^
                  -l "jmeter\\results\\result.jtl" ^
                  -e -o "jmeter\\report"
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
            echo "✅ Pipeline Finished Successfully!"
        }
        failure {
            echo "❌ Pipeline Failed! Check Console Output."
        }
    }
}
