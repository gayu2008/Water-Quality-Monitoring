pipeline {
    agent any

    triggers {
        // // Optional: runs automatically every ~15 minutes without a webhook or Poll SCM
        // pollSCM('') // leave empty or remove if you only want cron-based runs
        // cron('H/15 * * * *')
    }

    environment {
        SSH_TARGET = 'vboxuser@192.168.1.24'
        SSH_KEY    = 'C:\\ProgramData\\Jenkins\\.ssh\\jenkins_ubuntu'
        APP_DIR    = '/home/vboxuser/Water-Quality-Monitoring'
    }

    stages {

        // Stage 1: Connect Jenkins to Ubuntu
        stage('Connect to Ubuntu') {
            steps {
                echo 'Connecting to Ubuntu Server...'
                bat """
                ssh -i "${SSH_KEY}" -o IdentitiesOnly=yes -o StrictHostKeyChecking=no -o BatchMode=yes ${SSH_TARGET} "hostname && whoami"
                """
            }
        } // <-- Fixed missing brace

        // Stage 2: Pull latest code from GitHub
        stage('Git Pull') {
            steps {
                echo 'Pulling latest code from GitHub...'
                bat """
                ssh -i "${SSH_KEY}" -o IdentitiesOnly=yes -o StrictHostKeyChecking=no -o BatchMode=yes ${SSH_TARGET} "cd ${APP_DIR} && git pull --ff-only origin master"
                """
            }
        }

        // Stage 3: Build Spring Boot backend
        stage('Build Backend') {
            steps {
                echo 'Building Spring Boot Backend...'
                bat """
                ssh -i "${SSH_KEY}" -o IdentitiesOnly=yes -o StrictHostKeyChecking=no -o BatchMode=yes ${SSH_TARGET} "cd ${APP_DIR}/backend && mvn clean package -DskipTests"
                """
            }
        }

        // Stage 4: Build React frontend
        stage('Build Frontend') {
            steps {
                echo 'Building React Frontend...'
                bat """
                ssh -i "${SSH_KEY}" -o IdentitiesOnly=yes -o StrictHostKeyChecking=no -o BatchMode=yes ${SSH_TARGET} "cd ${APP_DIR} && npm ci && npm run build"
                """
            }
        }

        // Stage 5: Verify build artifacts
        stage('Verify Build') {
            steps {
                echo 'Verifying generated build files...'
                bat """
                ssh -i "${SSH_KEY}" -o IdentitiesOnly=yes -o StrictHostKeyChecking=no -o BatchMode=yes ${SSH_TARGET} "ls -lh ${APP_DIR}/backend/target/*.jar && ls -ld ${APP_DIR}/build"
                """
            }
        }

        // Stage 6: Automated Deployment on Ubuntu
        stage('Deploy') {
            steps {
                echo 'Deploying Spring Boot backend and React frontend...'
                bat """
                ssh -i "${SSH_KEY}" -o IdentitiesOnly=yes -o StrictHostKeyChecking=no -o BatchMode=yes ${SSH_TARGET} "sudo systemctl restart water-quality-backend && sudo systemctl is-active water-quality-backend"
                """
            }
        }
    }

    post {
        success {
            echo 'Water Quality Monitoring build and deployment completed successfully!'
        }

        failure {
            echo 'Pipeline failed. Please check Console Output.'
        }
    }
}