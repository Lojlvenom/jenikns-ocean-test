pipeline {
    agent any
    
    environment {
        PYTHON_VERSION = '3.10'  
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
        
        stage('Install Python') {
            steps {
                script {
                    try {
                        // Check if Python is installed
                        def pythonCheck = sh(script: 'command -v python3 || command -v python', returnStatus: true)
                        
                        if (pythonCheck != 0) {
                            echo 'Python not found. Installing Python...'
                            
                            // Try different package managers to install Python
                            def installAttempt = sh(script: '''
                                if command -v apt-get >/dev/null; then
                                    sudo apt-get update -qq
                                    sudo apt-get install -y python${PYTHON_VERSION} python${PYTHON_VERSION}-dev python3-pip
                                elif command -v yum >/dev/null; then
                                    sudo yum install -y python${PYTHON_VERSION} python${PYTHON_VERSION}-devel python3-pip
                                elif command -v dnf >/dev/null; then
                                    sudo dnf install -y python${PYTHON_VERSION} python${PYTHON_VERSION}-devel python3-pip
                                elif command -v zypper >/dev/null; then
                                    sudo zypper install -y python${PYTHON_VERSION} python${PYTHON_VERSION}-devel python3-pip
                                else
                                    echo "Unsupported package manager"
                                    exit 1
                                fi
                            ''', returnStatus: true)
                            
                            if (installAttempt != 0) {
                                error 'Failed to install Python'
                            }
                            
                            // Verify installation
                            sh 'python3 --version || python --version'
                        } else {
                            echo 'Python is already installed'
                            sh 'python3 --version || python --version'
                        }
                    } catch (Exception e) {
                        error "Python installation failed: ${e.getMessage()}"
                    }
                }
            }
        }
        
        stage('Setup') {
            steps {
                sh 'python3 -m pip install --upgrade pip || python -m pip install --upgrade pip'
                sh 'python3 -m pip install -r requirements.txt || python -m pip install -r requirements.txt'
            }
        }
        
        stage('Test') {
            steps {
                sh 'python3 -m pytest tests/ --cov=calculator --cov-report=xml:coverage.xml || python -m pytest tests/ --cov=calculator --cov-report=xml:coverage.xml'
            }
            post {
                always {
                    junit '**/junit.xml'
                    cobertura(coberturaReportFile: '**/coverage.xml')
                }
            }
        }
        
        stage('Build') {
            steps {
                echo 'Building Python package...'
            }
        }
        
        stage('Deploy') {
            when {
                branch 'main'
            }
            steps {
                echo 'Deploying to production...'
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline completed - cleanup can go here'
        }
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}