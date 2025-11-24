pipeline {
    agent any

    tools {
        maven 'maven'
    }

    stages {

        stage("Clone Code") {
            steps {
                echo "Cloning Repository..."
                git branch: 'main', url: 'https://github.com/srilakshmi0602/movie-ticket.git'
            }
        }

        stage("Build JAR") {
            steps {
                echo "Building Application..."
                sh "mvn clean package -DskipTests"
            }
            post {
                success {
                    echo "Build Successful ✔"
                }
                failure {
                    echo "Build Failed ❌"
                }
            }
        }

        stage("Stop Existing App") {
            steps {
                echo "Stopping currently running application on EC2..."
                sh """
                    ssh -o StrictHostKeyChecking=no -i /var/lib/jenkins/.ssh/sri_jsp.pem ubuntu@65.1.73.32 \
                    'pkill -f movie-ticket || true'
                """
            }
        }

        stage("Copy JAR to EC2") {
            steps {
                echo "Copying JAR to EC2 instance..."
                sh """
                    scp -o StrictHostKeyChecking=no -i /var/lib/jenkins/.ssh/sri_jsp.pem \
                    target/*.jar ubuntu@65.1.73.32:/home/ubuntu/movie-ticket.jar
                """
            }
        }

        stage("Start Application") {
            steps {
                echo "Starting application on EC2..."
                sh """
                    ssh -o StrictHostKeyChecking=no -i /var/lib/jenkins/.ssh/sri_jsp.pem ubuntu@65.1.73.32 \
                    'nohup java -jar movie-ticket.jar > app.log 2>&1 &'
                """
            }
        }

        stage("Done") {
            steps {
                echo "Deployment Completed 🎉"
            }
        }

    }

    post {
        always {
            echo "Pipeline finished running."
        }
        success {
            echo "🚀 Deployment Successful — Application live on port 8080"
        }
        failure {
            echo "❌ Deployment Failed — Check SSH key, server connectivity, and Jenkins permissions"
        }
    }
}
