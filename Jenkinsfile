pipeline {
    agent {
        docker {
            image 'python:3.10'
            args '-u root'
        }
    }

    stages {
        stage('Install Dependencies') {
            steps {
                echo "📦 Installing dependencies..."
                sh 'pip install --upgrade pip'
                sh 'pip install --no-cache-dir -r requirements.txt'
            }
        }

        stage('Run Tests') {
            steps {
                echo "🧪 Running tests..."
                // Gunakan python -m pytest agar pasti di environment Python yang benar
                sh 'python -m pytest test_app.py'
            }
        }

        stage('Deploy') {
            when {
                anyOf {
                    branch 'main'
                    branch pattern: "release/.*", comparator: "REGEXP"
                }
            }
            steps {
                echo "🚀 Simulating deploy from branch ${env.BRANCH_NAME}"
            }
        }
    }

    post {
        success {
            script {
                def payload = [
                    content: "✅ Build SUCCESS on `${env.BRANCH_NAME}`\n🔗 URL: ${env.BUILD_URL}"
                ]
                httpRequest(
                    httpMode: 'POST',
                    contentType: 'APPLICATION_JSON',
                    requestBody: groovy.json.JsonOutput.toJson(payload),
                    url: 'https://discord.com/api/webhooks/1425405267704807518/aFyYGvXiAnM2-uUQMwRHaSfrHqjfunuUyXBZvZDaQ7-R0enaIV_M6GcQYKlaxIm3PlMA'
                )
            }
        }

        failure {
            script {
                def payload = [
                    content: "❌ Build FAILED on `${env.BRANCH_NAME}`\n🔗 URL: ${env.BUILD_URL}"
                ]
                httpRequest(
                    httpMode: 'POST',
                    contentType: 'APPLICATION_JSON',
                    requestBody: groovy.json.JsonOutput.toJson(payload),
                    url: 'https://discord.com/api/webhooks/1425405267704807518/aFyYGvXiAnM2-uUQMwRHaSfrHqjfunuUyXBZvZDaQ7-R0enaIV_M6GcQYKlaxIm3PlMA'
                )
            }
        }
    }
}
