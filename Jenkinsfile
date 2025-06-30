pipeline {
    agent any
    
    environment {
        PYTHON = 'python3'
        VENV_DIR = "${WORKSPACE}/venv"
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[
                        url: 'https://github.com/Lojlvenom/jenikns-ocean-test.git',
                        credentialsId: ''
                    ]],
                    extensions: [[
                        $class: 'CleanBeforeCheckout'
                    ]]
                ])
            }
        }
        
        stage('Setup Virtual Environment') {
            steps {
                script {
                    sh """
                    ${PYTHON} -m venv ${VENV_DIR}
                    . ${VENV_DIR}/bin/activate
                    python --version
                    """
                }
            }
        }
        
        stage('Install Dependencies') {
            steps {
                script {
                    sh """
                    . ${VENV_DIR}/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                    """
                }
            }
        }
        
        stage('Run Tests') {
            steps {
                script {
                    sh """
                    . ${VENV_DIR}/bin/activate
                    python -m pytest tests/ --cov=calculator --cov-report=xml:coverage.xml --junitxml=test-results.xml
                    """
                }
            }
            post {
                always {
                    junit 'test-results.xml'
                    archiveArtifacts artifacts: 'coverage.xml', fingerprint: true
                }
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline completed'
            cleanWs()
        }
        failure {
            echo 'Pipeline failed - check the logs'
        }
    }
}