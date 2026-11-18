
pipeline {
    agent any

    stages {

        // Stage 1: Connect Jenkins to Ubuntu
        stage('Connect to Ubuntu') {
            steps {
                echo 'Connecting to Ubuntu Server...'

                bat '''
                ssh -i "C:\\ProgramData\\Jenkins\\.ssh\\jenkins_ubuntu" -o IdentitiesOnly=yes -o StrictHostKeyChecking=no -o BatchMode=yes vboxuser@192.168.1.24 "hostname && whoami"
                '''
            }
        }

        // Stage 2: Pull latest code from GitHub
        stage('Git Pull') {
            steps {
                echo 'Pulling latest code from GitHub...'

                bat '''
                ssh -i "C:\\ProgramData\\Jenkins\\.ssh\\jenkins_ubuntu" -o IdentitiesOnly=yes -o StrictHostKeyChecking=no -o BatchMode=yes vboxuser@192.168.1.24 "cd /home/vboxuser/Water-Quality-Monitoring && git pull --ff-only origin master"
                '''
            }
        }

        // Stage 3: Build Spring Boot backend
        stage('Build Backend') {
            steps {
                echo 'Building Spring Boot Backend...'

                bat '''
                ssh -i "C:\\ProgramData\\Jenkins\\.ssh\\jenkins_ubuntu" -o IdentitiesOnly=yes -o StrictHostKeyChecking=no -o BatchMode=yes vboxuser@192.168.1.24 "cd /home/vboxuser/Water-Quality-Monitoring/backend && mvn clean package -DskipTests"
                '''
            }
        }

        // Stage 4: Build React frontend
        stage('Build Frontend') {
            steps {
                echo 'Building React Frontend...'

                bat '''
                ssh -i "C:\\ProgramData\\Jenkins\\.ssh\\jenkins_ubuntu" -o IdentitiesOnly=yes -o StrictHostKeyChecking=no -o BatchMode=yes vboxuser@192.168.1.24 "cd /home/vboxuser/Water-Quality-Monitoring && npm ci && npm run build"
                '''
            }
        }

        // Stage 5: Verify build artifacts
        stage('Verify Build') {
            steps {
                echo 'Verifying generated build files...'

                bat '''
                ssh -i "C:\\ProgramData\\Jenkins\\.ssh\\jenkins_ubuntu" -o IdentitiesOnly=yes -o StrictHostKeyChecking=no -o BatchMode=yes vboxuser@192.168.1.24 "ls -lh /home/vboxuser/Water-Quality-Monitoring/backend/target/*.jar && ls -ld /home/vboxuser/Water-Quality-Monitoring/build"
                '''
            }
        }
    }

    post {
        success {
            echo 'Water Quality Monitoring build completed successfully!'
        }

        failure {
            echo 'Pipeline failed. Please check Console Output.'
        }
    }
}
