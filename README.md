# OrangeHRM Performance Testing Project 🚀

This repository contains a complete Performance Testing framework for **OrangeHRM** using modern Performance Engineering tools:

- Apache JMeter (Load & Performance Testing)
- InfluxDB (Metrics Storage)
- Grafana (Real-time Monitoring Dashboard)
- Jenkins (CI Pipeline Automation)

---

## 📌 Project Structure

```text
OrangeHRM-PerformanceProject/
│
├── jmeter/                             # JMeter test plans (.jmx)
│   ├── orangehrm_login_load_test.jmx   # Login Load Test
│   ├── results/                        # Test results (.jtl)
│   └── report/                         # Generated HTML report
│
├── performance-monitoring/             # Docker compose (Grafana + InfluxDB)
│   └── docker-compose.yml
│
├── Jenkinsfile                         # Jenkins Pipeline automation
├── .gitignore                          # Ignore test output files
└── README.md                           # Project documentation


🚀 Run Performance Test Locally

Run JMeter in non-GUI mode:

jmeter -n ^
  -t jmeter/orangehrm_login_load_test.jmx ^
  -l jmeter/results/result.jtl ^
  -e -o jmeter/report

After execution, open the generated HTML report:
jmeter/report/index.html

📊 Monitoring Stack (Grafana + InfluxDB)

The monitoring stack is provided inside:
performance-monitoring/

Start the monitoring services:
cd performance-monitoring
docker compose up -d

Access services:

Grafana Dashboard: http://localhost:3000
InfluxDB Database: http://localhost:8086

Default Grafana login:

Username: admin
Password: admin

🤖 Jenkins CI Pipeline

This repository includes a ready-to-run Jenkins Pipeline.

The pipeline automatically:

Executes the JMeter load test
Generates the HTML performance report
Publishes the report inside Jenkins
Sends metrics into InfluxDB for Grafana visualization

Once the build finishes, Jenkins will display:

✅ OrangeHRM Performance Test Report




✅ Key Skills Demonstrated

Load testing with Apache JMeter
Backend Listener metrics storage in InfluxDB
Real-time monitoring with Grafana dashboards
CI automation using Jenkins Pipeline
Professional Performance Engineering portfolio project

👤 Author

OrangeHRM Performance Testing Project
Built for learning, portfolio, and DevOps/Performance Engineering practice.

