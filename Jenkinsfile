pipeline {
    agent any

    environment {
        DEPLOY_BASE = "/var/www/releases"         // all release folders go here
        SYMLINK = "/root/django-todo-react"       // services point here
        RELEASE = "release-${BUILD_NUMBER}"       // new release folder name
        REPO = "https://github.com/GAURAVJAIN2498/django-todo-react.git" // your repo
        BRANCH = "main"
        SERVER = "root@54.224.61.18"              // prod server
    }

    stages {
        stage('Deployment Code') {
            steps {
                sshagent(['ssh-cred']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no $SERVER "
                        set -e
                        unlink $SYMLINK
                        # make release dir
                        mkdir -p $DEPLOY_BASE/$RELEASE &&
                        cd $DEPLOY_BASE/$RELEASE &&
                        
                        # clone fresh code
                        git clone -b $BRANCH $REPO . &&
                        
                       ln -sfn $DEPLOY_BASE/$RELEASE $SYMLINK
                    "
                    '''
                }
            }
        }

        stage('Setup Backend') {
            steps {
                sshagent(['ssh-cred']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no $SERVER "
                        set -e
                        
                        cd $SYMLINK/backend &&
                        pipenv install --deploy &&
                
                        
                        sudo systemctl daemon-reload &&
                        sudo systemctl restart backend.service
                    "
                    '''
                }
            }
        }

        stage('Setup Frontend') {
            steps {
                sshagent(['ssh-cred']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no $SERVER "
                        set -e
                        
                        cd $SYMLINK/frontend &&
                        npm install &&
                        
                        sudo systemctl daemon-reload &&
                        sudo systemctl restart frontend.service
                    "
                    '''
                }
            }
        }

        stage('Check Active Release') {
            steps {
                sshagent(['ssh-cred']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no $SERVER "
                        echo 'Current active release:'
                        readlink -f $SYMLINK
                    "
                    '''
                }
            }
        }
    }
}
