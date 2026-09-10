pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                echo 'Recuperation du code source'
                checkout scm
            }
        }
        stage('Build / Preparation') {
            steps {
                echo 'Preparation de l environnement'
                sh 'echo Build OK'
            }
        }
        stage('Security Analysis - SAST') {
            steps {
                echo 'Lancement Semgrep (SAST)'
                sh 'docker run --rm -v $(pwd):/src returntocorp/semgrep semgrep --config=auto /src --json --output /src/reports/semgrep-results.json'
            }
        }
        stage('Additional Security Check - SCA + Secrets') {
            steps {
                echo 'Lancement Trivy (SCA)'
                sh 'docker run --rm -v $(pwd):/app aquasec/trivy fs /app --output /app/reports/trivy-scan-results.txt'
                echo 'Lancement Gitleaks (Secret Detection)'
                sh 'docker run --rm -v $(pwd):/repo zricethezav/gitleaks detect --source=/repo --report-path=/repo/reports/gitleaks-report.json'
            }
        }
        stage('Report Generation') {
            steps {
                echo 'Rapports generes dans le dossier reports/'
                archiveArtifacts artifacts: 'reports/*', allowEmptyArchive: true
            }
        }
        stage('Notification') {
            steps {
                echo 'Pipeline termine - notification envoyee (simulee)'
            }
        }
    }
}