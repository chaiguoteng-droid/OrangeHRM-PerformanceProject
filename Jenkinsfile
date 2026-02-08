pipeline {
    agent any

    environment {
        // JMeter executable path (Windows)
        JMETER_BIN = "D:\\JMeter\\apache-jmeter-5.6.3\\bin\\jmeter.bat"

        // Force Java 21 for Jenkins pipeline execution
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
                echo ==========================================
                echo Running OrangeHRM Login Load Test...
                echo ==========================================

                REM ------------------------------------------------
                REM Force pipeline to use Java 21 (Jenkins may use Java 24 by default)
                REM ------------------------------------------------
                set JAVA_HOME=%JAVA21_HOME%
                set PATH=%JAVA_HOME%\\bin;%PATH%

                echo Using Java version:
                java -version

                REM ------------------------------------------------
                REM Prepare folders
                REM ------------------------------------------------
                if not exist "jmeter\\results" mkdir "jmeter\\results"

                if exist "jmeter\\report" (
                    echo Deleting old report folder...
                    rmdir /s /q "jmeter\\report"
                )

                REM ------------------------------------------------
                REM Run JMeter in Non-GUI mode
                REM ------------------------------------------------
                echo Starting JMeter Test...

                "%JMETER_BIN%" -n ^
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
                echo "Archiving JMeter result files..."
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
                    keepAl
