pipeline {
    agent any

    stages {
        stage('Deploy Backend') {
            steps {
                sshagent(['ssh-cred']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no root@3.80.69.87 "
                        cd /root/django-todo-react &&
                        pipenv install --deploy &&
                        sudo systemctl daemon-reload &&
                        sudo systemctl restart backend.service
                    "
                    '''
                }
            }
        }

        stage('Deploy Frontend') {
            steps {
                sshagent(['ssh-cred']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no root@3.80.69.87 "
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
