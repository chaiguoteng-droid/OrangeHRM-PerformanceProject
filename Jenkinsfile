pipeline {
    agent any

    environment {
        JAVA21_HOME = "C:\\Program Files\\Java\\jdk-21"

        // Directly use the JMeter jar (avoids bat issues in Jenkins)
        JMETER_JAR = "D:\\JMeter\\apache-jmeter-5.6.3\\bin\\ApacheJMeter.jar"
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

                REM Force Java 21
                set JAVA_HOME=%JAVA21_HOME%
                set PATH=%JAVA_HOME%\\bin;%PATH%

                java -version

                REM Prepare folders
                if not exist "jmeter\\results" mkdir "jmeter\\results"
                if exist "jmeter\\report" rmdir /s /q "jmeter\\report"

                REM Run JMeter directly via jar (MOST STABLE)
                java -jar "%JMETER_JAR%" -n ^
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
            echo "✅ Pipeline Finished Successfully!"
        }
        failure {
            echo "❌ Pipeline Failed! Check Console Output."
        }
    }
}
