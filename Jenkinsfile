pipeline {
    agent any

    stages {
        stage('Clone Code') {
            steps {
                git branch: 'main', url: 'https://github.com/srilakshmi0602/movie-ticket.git'
            }
        }

        stage('Build JAR') {
            steps {
                sh "mvn clean package -DskipTests"
            }
        }

        stage('Stop Existing App') {
            steps {
                sh "ssh -o StrictHostKeyChecking=no -i /var/lib/jenkins/.ssh/sri_jsp.pem ubuntu@65.1.73.32 'pkill -f movie-ticket || true'"
            }
        }

        stage('Copy JAR') {
            steps {
                sh "scp -o StrictHostKeyChecking=no -i /var/lib/jenkins/.ssh/sri_jsp.pem target/*.jar ubuntu@65.1.73.32:/home/ubuntu/movie-ticket.jar"
            }
        }

        stage('Start App') {
            steps {
                sh """
                    ssh -o StrictHostKeyChecking=no -i /var/lib/jenkins/.ssh/sri_jsp.pem ubuntu@65.1.73.32 "nohup java -jar movie-ticket.jar > app.log 2>&1 &"
                """
            }
        }
    }

    post {
        success {
            echo "🚀 Deployment Successful! Application running on port 8080"
        }
        failure {
            echo "❌ Deployment Failed — Check SSH connection, key, and IP address."
        }
        always {
            echo "Pipeline execution completed."
        }
    }
}
