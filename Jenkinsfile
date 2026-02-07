pipeline {
    agent any

    stages {

        stage("Checkout") {
            steps {
                checkout scm
            }
        }

        stage("Run JMeter Load Test (Docker)") {
            steps {
                echo "🚀 Running OrangeHRM Login Load Test using Docker JMeter..."

                sh """
                echo "=========================================="
                echo "Preparing folders..."
                echo "=========================================="

                mkdir -p jmeter/results
                rm -rf jmeter/report

                echo "=========================================="
                echo "Executing JMeter Test Plan..."
                echo "=========================================="

                docker run --rm \
                  -v \$(pwd):/tests \
                  justb4/jmeter \
                  -n \
                  -t /tests/jmeter/testplans/orangehrm_login_load_test.jmx \
                  -l /tests/jmeter/results/result.jtl \
                  -e -o /tests/jmeter/report
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
            echo "📦 Archiving artifacts..."

            archiveArtifacts artifacts: 'jmeter/results/*.jtl', fingerprint: true
            archiveArtifacts artifacts: 'jmeter/report/**', fingerprint: false
        }

        success {
            echo "✅ Performance Test Completed Successfully!"
        }

        failure {
            echo "❌ Performance Test Failed! Check console output."
        }
    }
}
