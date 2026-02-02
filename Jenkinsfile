pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/dockrphage/devops-monitoring-lab.git', branch: 'main'
            }
        }

        stage('Get EC2 Private IP') {
            steps {
                script {
                    TOKEN = sh(
                        script: 'curl -s -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"',
                        returnStdout: true
                    ).trim()

                    EC2_IP = sh(
                        script: 'curl -s -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/local-ipv4',
                        returnStdout: true
                    ).trim()

                    echo "EC2_IP = ${EC2_IP}"
                }
            }
        }

        stage('Run Prometheus') {
            steps {
                sh '''
                docker rm -f prometheus || true

                docker run -d \
                  --name prometheus \
                  -p 9090:9090 \
                  -v "$WORKSPACE/prometheus/prometheus.yml:/etc/prometheus/prometheus.yml" \
                  --add-host=host.docker.internal:$EC2_IP \
                  prom/prometheus
                '''
            }
        }
    }
}

