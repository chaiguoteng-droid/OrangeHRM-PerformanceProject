pipeline {
    agent any

    stages {

        stage("Checkout") {
            steps {
                checkout scm
            }
        }

        stage("Run JMeter Load Test") {
            steps {
                bat '''
                echo ==========================================
                echo Running OrangeHRM Login Load Test...
                echo ==========================================

                REM Force Java 21
                set JAVA_HOME=C:\\Program Files\\Java\\jdk-21
                set PATH=%JAVA_HOME%\\bin;%PATH%

                REM Prepare folders
                if not exist "jmeter\\results" mkdir "jmeter\\results"

                REM Clean old result file
                if exist "jmeter\\results\\result.jtl" del /q "jmeter\\results\\result.jtl"

                REM Delete old report folder
                if exist "jmeter\\report" rmdir /s /q "jmeter\\report"

                echo Starting JMeter execution...

                REM Run JMeter with XML output (MOST STABLE)
                java -jar "D:\\JMeter\\apache-jmeter-5.6.3\\bin\\ApacheJMeter.jar" ^
                  -n ^
                  -t "jmeter\\testplans\\orangehrm_login_load_test.jmx" ^
                  -l "jmeter\\results\\result.jtl" ^
                  -Jjmeter.save.saveservice.output_format=xml ^
                  -e -o "jmeter\\report"

                echo ==========================================
                echo JMeter Test Completed Successfully!
                echo ==========================================
                '''
            }
        }

        stage("Archive Results") {
            steps {
                archiveArtifacts artifacts: "jmeter/results/result.jtl", fingerprint: true
            }
        }

        stage("Publish HTML Dashboard") {
            steps {
                publishHTML(target: [
                    reportDir: "jmeter/report",
                    reportFiles: "index.html",
                    reportName: "OrangeHRM JMeter Performance Report",
                    keepAll: true,
                    alwaysLinkToLastBuild: true
                ])
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline SUCCESS! JMeter Dashboard Generated."
        }
        failure {
            echo "❌ Pipeline FAILED! Check Console Output."
        }
    }
}

