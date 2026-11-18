
pipeline {
    agent any

    stages {

       
stage('Connect to Ubuntu') {
    steps {
        echo 'Connecting to Ubuntu Server...'

        withCredentials([
            sshUserPrivateKey(
                credentialsId: 'ubuntu-ssh-key',
                keyFileVariable: 'SSH_KEY',
                usernameVariable: 'SSH_USER'
            )
        ]) {
            bat '''
            icacls "%SSH_KEY%" /inheritance:r /grant:r "DESKTOP-KJCSDVI\\CRTSYS001:R"

            ssh -i "%SSH_KEY%" -o StrictHostKeyChecking=no %SSH_USER%@192.168.1.24 "hostname && whoami"
            '''
        }
    }
}


        stage('Git Pull') {
            steps {
                echo 'Pulling latest code from GitHub...'

                withCredentials([
                    sshUserPrivateKey(
                        credentialsId: 'ubuntu-ssh-key',
                        keyFileVariable: 'SSH_KEY',
                        usernameVariable: 'SSH_USER'
                    )
                ]) {
                    bat '''
                    ssh -i "%SSH_KEY%" %SSH_USER%@192.168.1.24 "cd /home/vboxuser/Water-Quality-Monitoring && git pull --ff-only origin main"
                    '''
                }
            }
        }

        stage('Build Backend') {
            steps {
                echo 'Building Spring Boot Backend...'

                withCredentials([
                    sshUserPrivateKey(
                        credentialsId: 'ubuntu-ssh-key',
                        keyFileVariable: 'SSH_KEY',
                        usernameVariable: 'SSH_USER'
                    )
                ]) {
                    bat '''
                    ssh -i "%SSH_KEY%" %SSH_USER%@192.168.1.24 "cd /home/vboxuser/Water-Quality-Monitoring/backend && mvn clean package -DskipTests"
                    '''
                }
            }
        }

        stage('Build Frontend') {
            steps {
                echo 'Building React Frontend...'

                withCredentials([
                    sshUserPrivateKey(
                        credentialsId: 'ubuntu-ssh-key',
                        keyFileVariable: 'SSH_KEY',
                        usernameVariable: 'SSH_USER'
                    )
                ]) {
                    bat '''
                    ssh -i "%SSH_KEY%" %SSH_USER%@192.168.1.24 "cd /home/vboxuser/Water-Quality-Monitoring && npm ci && npm run build"
                    '''
                }
            }
        }

        stage('Verify Build') {
            steps {
                echo 'Verifying generated files...'

                withCredentials([
                    sshUserPrivateKey(
                        credentialsId: 'ubuntu-ssh-key',
                        keyFileVariable: 'SSH_KEY',
                        usernameVariable: 'SSH_USER'
                    )
                ]) {
                    bat '''
                    ssh -i "%SSH_KEY%" %SSH_USER%@192.168.1.24 "ls -lh /home/vboxuser/Water-Quality-Monitoring/backend/target/*.jar && ls -ld /home/vboxuser/Water-Quality-Monitoring/build"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Water Quality Monitoring build successful!'
        }

        failure {
            echo 'Pipeline failed. Check Console Output.'
        }
    }
}
