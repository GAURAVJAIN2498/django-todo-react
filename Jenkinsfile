pipeline {
    agent any

    stages {
        stage('Deployment backend') {
            steps {
                sshagent(['ssh-cred']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no root@54.224.61.18 "
                        cd /root/django-todo-react &&
                        pipenv install --deploy &&
                        sudo systemctl daemon-reload &&
                        sudo systemctl restart backend.service
                    "
                    '''
                }
            }
        }

        stage('Deployment Frontend') {
            steps {
                sshagent(['ssh-cred']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no root@54.224.61.18 "
                        cd /root/django-todo-react/frontend &&
                        sudo systemctl daemon-reload &&
                        sudo systemctl restart frontend.service
                    "
                    '''
                }
            }
        }
    }
}
