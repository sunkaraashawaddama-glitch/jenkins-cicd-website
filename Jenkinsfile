

pipeline {
    agent any

    options {
        timestamps()
    }

    environment {
        WEBSITE_HOST = 'WEBSITE_PUBLIC_IP'
        WEBSITE_USER = 'deploy'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/sunkaraashawaddama-glitch/jenkins-cicd-website.git'
            }
        }

        stage('Validate') {
            steps {
                sh '''
                    set -eu
                    test -s index.html
                    grep -qi '<html' index.html
                    echo "Validation successful"
                '''
            }
        }

        stage('Deploy') {
            steps {
                withCredentials([
                    sshUserPrivateKey(
                        credentialsId: 'website-server-ssh',
                        keyFileVariable: 'SSH_KEY',
                        usernameVariable: 'SSH_USER'
                    )
                ]) {
                    sh '''
                        set -eu
                        mkdir -p "$HOME/.ssh"
                        chmod 700 "$HOME/.ssh"

                        ssh-keyscan -H "$WEBSITE_HOST" \
                            >> "$HOME/.ssh/known_hosts"

                        scp -i "$SSH_KEY" \
                            index.html \
                            "$SSH_USER@$WEBSITE_HOST:/tmp/index.html"

                        ssh -i "$SSH_KEY" "$SSH_USER@$WEBSITE_HOST" \
                            'install -m 644 /tmp/index.html /var/www/html/index.html'
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Website deployment successful!'
        }
        failure {
            echo 'Pipeline failed. Check the stage console output.'
        }
    }
}
