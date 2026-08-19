pipeline {
    agent any

    parameters {
        string(
            name: 'LANGUAGE',
            defaultValue: 'java',
            description: 'Programming language for Dockerfile generation'
        )
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Verify Environment') {
            steps {
                sh '''
                    echo "Checking environment..."

                    python3 --version
                    docker --version
                    ollama --version

                    echo "Available Ollama models:"
                    ollama list
                '''
            }
        }

        stage('Setup Python Environment') {
            steps {
                sh '''
                    python3 -m venv venv

                    venv/bin/python -m pip install --upgrade pip

                    venv/bin/pip install -r requirements.txt
                '''
            }
        }

        stage('Generate Dockerfile with GenAI') {
            steps {
                sh '''
                    echo "Generating Dockerfile for: ${LANGUAGE}"

                    venv/bin/python generate_dockerfile.py "${LANGUAGE}"
                '''
            }
        }

        stage('Verify Generated Dockerfile') {
            steps {
                sh '''
                    if [ ! -f Dockerfile ]; then
                        echo "ERROR: Dockerfile was not generated."
                        exit 1
                    fi

                    echo "===== Generated Dockerfile ====="
                    cat Dockerfile
                    echo "================================"
                '''
            }
        }
    }

    post {
        success {
            echo 'GenAI Dockerfile generation completed successfully.'
        }

        failure {
            echo 'GenAI pipeline failed.'
        }
    }
}