pipeline {
    agent any

    environment {
        // Force Java 21 (Stable for Jenkins + JMeter)
        JAVA21_HOME = "C:\\Program Files\\Java\\jdk-21"

        // Run JMeter directly via jar (avoids bat issues in Jenkins)
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

                REM ------------------------------------------------
                REM Force Java 21
                REM ------------------------------------------------
                set JAVA_HOME=%JAVA21_HOME%
                set PATH=%JAVA_HOME%\\bin;%PATH%

                echo Using Java version:
                java -version

                REM ------------------------------------------------
                REM Prepare folders
                REM ------------------------------------------------
                if not exist "jmeter\\results" mkdir "jmeter\\results"

                REM ------------------------------------------------
                REM Delete old JTL result file (Fix: file not empty)
                REM ------------------------------------------------
                if exist "jmeter\\results\\result.jtl" (
                    echo Deleting old result.jtl...
                    del /q "jmeter\\results\\result.jtl"
                )

                REM ------------------------------------------------
                REM Delete old HTML report folder
                REM ------------------------------------------------
                if exist "jmeter\\report" (
                    echo Deleting old report folder...
                    rmdir /s /q "jmeter\\report"
                )

                REM ------------------------------------------------
                REM Run JMeter Test (Non-GUI)
                REM ------------------------------------------------
                echo Starting JMeter execution...

                java -jar "%JMETER_JAR%" -n ^
                  -t "jmeter\\testplans\\orangehrm_login_load_test.jmx" ^
                  -l "jmeter\\results\\result.jtl" ^
                  -e -o "jmeter\\report"

                echo ==========================================
                echo JMeter Test Completed Successfully!
                echo ==========================================
                """
            }
        }

        stage("Archive Test Results") {
            steps {
                echo "Archiving JMeter JTL results..."
                archiveArtifacts artifacts: 'jmeter/results/*.jtl', fingerprint: true
            }
        }

        stage("Publish HTML Report") {
            steps {
                echo "Publishing JMeter HTML Dashboard..."

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
            echo "✅ Pipeline SUCCESS! Performance Test Report Generated."
        }

        failure {
            echo "❌ Pipeline FAILED! Please check Console Output."
        }

        always {
            echo "Build finished."
        }
    }
}
