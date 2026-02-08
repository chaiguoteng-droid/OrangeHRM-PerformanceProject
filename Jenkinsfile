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
                bat """
                echo ==========================================
                echo Running OrangeHRM Login Load Test...
                echo ==========================================

                REM Force Java 21
                set JAVA_HOME=C:\\Program Files\\Java\\jdk-21
                set PATH=%JAVA_HOME%\\bin;%PATH%

                java -version

                REM FULL CLEAN (important)
                if exist "jmeter\\results" rmdir /s /q "jmeter\\results"
                mkdir "jmeter\\results"

                if exist "jmeter\\report" rmdir /s /q "jmeter\\report"

                echo Starting JMeter execution...

                REM Run JMeter with CSV output (Required for Dashboard)
                java -jar "D:\\JMeter\\apache-jmeter-5.6.3\\bin\\ApacheJMeter.jar" ^
                  -n ^
                  -t "jmeter\\testplans\\orangehrm_login_load_test.jmx" ^
                  -l "jmeter\\results\\result.jtl" ^
                  -e -o "jmeter\\report" ^
                  -Jjmeter.save.saveservice.output_format=csv ^
                  -Jjmeter.save.saveservice.print_field_names=true ^
                  -Jjmeter.save.saveservice.timestamp_format=ms

                echo ==========================================
                echo JMeter Test Completed Successfully!
                echo ==========================================
                """
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
            echo "✅ SUCCESS! Dashboard Published."
        }
        failure {
            echo "❌ FAILED! Check Console Output."
        }
    }
}

