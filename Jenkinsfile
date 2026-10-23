withCredentials([
    sshUserPrivateKey(
        credentialsId: 'website-server-ssh',
        keyFileVariable: 'SSH_KEY',
        usernameVariable: 'SSH_USER'
    )
]) {
    sh '''
        set -eu
        scp -i "$SSH_KEY" \
          -o StrictHostKeyChecking=yes \
          index.html \
          "$SSH_USER@$WEBSITE_HOST:/tmp/index.html"
    '''
}
