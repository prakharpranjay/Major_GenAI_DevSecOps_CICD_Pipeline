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
                powershell '''
                    Write-Host "=== Jenkins User ==="
                    whoami

                    Write-Host "=== Python ==="
                    & "C:\\Users\\Prakhar Pranjay\\AppData\\Local\\Programs\\Python\\Python312\\python.exe" --version

                    Write-Host "=== Docker ==="
                    & "C:\\Program Files\\Docker\\Docker\\resources\\bin\\docker.exe" --version

                    Write-Host "=== Ollama ==="
                    & "C:\\Users\\Prakhar Pranjay\\AppData\\Local\\Programs\\Ollama\\ollama.exe" --version

                    Write-Host "=== Ollama Models ==="
                    & "C:\\Users\\Prakhar Pranjay\\AppData\\Local\\Programs\\Ollama\\ollama.exe" list
                '''
            }
        }

        stage('Setup Python Environment') {
            steps {
                powershell '''
                    $python = "C:\\Users\\Prakhar Pranjay\\AppData\\Local\\Programs\\Python\\Python312\\python.exe"

                    if (!(Test-Path "venv")) {
                        & $python -m venv venv
                    }

                    .\\venv\\Scripts\\python.exe -m pip install --upgrade pip
                    .\\venv\\Scripts\\python.exe -m pip install -r requirements.txt
                '''
            }
        }

        stage('Generate Dockerfile with GenAI') {
            steps {
                powershell '''
                    Write-Host "Generating Dockerfile for: $env:LANGUAGE"

                    .\\venv\\Scripts\\python.exe generate_dockerfile.py "$env:LANGUAGE"
                '''
            }
        }

        stage('Verify Generated Dockerfile') {
            steps {
                powershell '''
                    if (!(Test-Path "Dockerfile")) {
                        Write-Error "Dockerfile was not generated."
                        exit 1
                    }

                    Write-Host "===== Generated Dockerfile ====="
                    Get-Content Dockerfile
                    Write-Host "================================"
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