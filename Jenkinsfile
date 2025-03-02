pipeline {
    agent any
    environment {
        SONAR_RUNNER_HOME = '/home/jenkins/sonar-scanner'
        PATH = "${SONAR_RUNNER_HOME}/bin:${env.PATH}"
        ZAP_API_KEY = credentials('ZAP_API_KEY') // Use the credentials ID you set in Jenkins
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/rthoma38/scanner.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                dir('anomaly_detection') {
                    sh '''
                        python3 -m venv venv
                        . venv/bin/activate
                        pip install -r requirements.txt
                    '''
                }
            }
        }

        stage('Data Collection') {
            steps {
                dir('anomaly_detection') {
                    sh '''
                        sudo tcpdump -i eth0 -c 1000 -w network_data.pcap
                        . venv/bin/activate
                        python3 process_data.py
                    '''
                }
            }
        }

        stage('Train Model') {
            steps {
                dir('anomaly_detection') {
                    sh '''
                        . venv/bin/activate
                        python3 train_model.py
                    '''
                }
            }
        }

        stage('Save Model') {
            steps {
                dir('anomaly_detection') {
                    sh '''
                        . venv/bin/activate
                        python3 save_model.py
                    '''
                }
            }
        }

        stage('Deploy Model') {
            steps {
                dir('anomaly_detection') {
                    sh '''
                        . venv/bin/activate
                        python3 deploy_model.py
                    '''
                }
            }
        }

        stage('Test Model') {
            steps {
                dir('anomaly_detection') {
                    sh '''
                        . venv/bin/activate
                        python3 test_model.py
                    '''
                }
            }
        }
    }
}
