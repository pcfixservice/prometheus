pipeline {
    agent any
    environment {
        PROMETHEUS_VERSION = "2.51.0"
        GRAFANA_VERSION = "10.0.1"
    }
    stages {
        stage('Checkout') {
            steps {
                echo 'Cloning repository...'
                git 'https://github.com/pcfixservice/prometheus.git'
            }
        }

        stage('Build Docker Images') {
            steps {
                echo 'Building Docker images...'
                sh 'docker-compose -f docker-compose.yml build'
            }
        }

        stage('Deploy Services') {
            steps {
                echo 'Starting Prometheus and Grafana...'
                sh 'docker-compose -f docker-compose.yml up -d'
            }
        }

        stage('Configure Prometheus') {
            steps {
                echo 'Copying Prometheus config and restarting...'
                sh 'docker cp prometheus.yml prometheus:/etc/prometheus/prometheus.yml || true'
                sh 'docker restart prometheus || true'
            }
        }

        stage('Configure Grafana') {
            steps {
                echo 'Copying dashboards and restarting Grafana...'
                sh 'docker cp dashboards/. grafana:/var/lib/grafana/dashboards/ || true'
                sh 'docker restart grafana || true'
            }
        }

        stage('Verify') {
            steps {
                echo 'Checking Prometheus and Grafana...'
                sh 'curl -s http://localhost:9090/targets | head -n 20'
                sh 'curl -s http://localhost:3000/api/health | head -n 20'
            }
        }
    }
}

