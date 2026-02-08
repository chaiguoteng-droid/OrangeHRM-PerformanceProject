pipeline {
    agent any

    environment {
        JAVA_HOME = "C:\\Program Files\\Java\\jdk-21"
        JMETER_JAR = "D:\\JMeter\\apache-jmeter-5.6.3\\bin\\ApacheJMeter.jar"
    }

    stages {

        stage("Checkout") {
            steps {
                checkout scm
            }
        }

        stage("Run JMeter Load Test + Generate Dashboard") {
            steps {
                bat """
                echo ===============================
                echo Running OrangeHRM Load Test...
                echo ===============================

                REM Force Java 21
                set PATH=%JAVA_HOME%\\bin;%PATH%
                java -version

                REM Prepare folders
                if not exist "jmeter\\results" mkdir "jmeter\\results"

                REM Clean old result + report
                if exist "jmeter\\results\\result.jtl" del /q "jmeter\\results\\result.jtl"
                if exist "jmeter\\report" rmdir /s /q "jmeter\\report"

                REM Run JMeter (CSV output default)
                java -jar "%JMETER_JAR%" ^
                  -n ^
                  -t "jmeter\\testplans\\orangehrm_login_load_test.jmx" ^
                  -l "jmeter\\results\\result.jtl" ^
                  -e -o "jmeter\\report"

                echo ===============================
                echo JMeter Dashboard Generated!
                echo ===============================
                """
            }
        }

        stage("Publish HTML Dashboard") {
            steps {
                publishHTML(target: [
                    reportDir: "jmeter/report",
                    reportFiles: "index.html",
                    reportName: "OrangeHRM JMeter Dashboard",
                    keepAll: true,
                    alwaysLinkToLastBuild: true,
                    allowMissing: false
                ])
            }
        }
    }

    post {
        success {
            echo "✅ SUCCESS: Dashboard available in Jenkins UI."
        }
        failure {
            echo "❌ FAILED: Check Console Output."
        }
    }
}

