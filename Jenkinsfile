pipeline {
    agent any

    environment {
        JAVA21_HOME = "C:\\Program Files\\Java\\jdk-21"
        JMETER_JAR  = "D:\\JMeter\\apache-jmeter-5.6.3\\bin\\ApacheJMeter.jar"
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

                REM -------------------------------
                REM FULL CLEAN (Fix empty graphs)
                REM -------------------------------
                if exist "jmeter\\results" rmdir /s /q "jmeter\\results"
                mkdir "jmeter\\results"

                if exist "jmeter\\report" rmdir /s /q "jmeter\\report"

                REM -------------------------------
                REM Run JMeter (CSV output is correct)
                REM -------------------------------
                java -jar "%JMETER_JAR%" -n ^
                  -t "jmeter\\testplans\\orangehrm_login_load_test.jmx" ^
                  -l "jmeter\\results\\result.jtl" ^
                  -e -o "jmeter\\report"

                echo JMeter Test Completed!
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
}

