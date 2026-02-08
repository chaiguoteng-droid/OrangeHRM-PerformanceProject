pipeline {
    agent any

    environment {
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
                echo Running OrangeHRM Login Load Test...

                if not exist jmeter\\results mkdir jmeter\\results
                if exist jmeter\\report rmdir /s /q jmeter\\report

                %JMETER_BIN% -n ^
                  -t jmeter\\testplans\\orangehrm_login_load_test.jmx ^
                  -l jmeter\\results\\result.jtl ^
                  -e -o jmeter\\report
                """
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

