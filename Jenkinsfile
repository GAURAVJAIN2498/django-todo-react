pipeline {
    agent any

    environment {
        DEPLOY_BASE = "/var/www/releases"         // all release folders go here
        SYMLINK = "/root/django-todo-react"       // services point here
        RELEASE = "release-${BUILD_NUMBER}"       // new release folder name
        REPO = "https://github.com/GAURAVJAIN2498/django-todo-react.git"
        BRANCH = "main"
        SERVER = "root@54.224.61.18"              // prod server
    }

    stages {
        stage('Deploy Code') {
            steps {
                sshagent(['ssh-cred']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no $SERVER "
                        set -e

                        # create new release dir
                        mkdir -p $DEPLOY_BASE/$RELEASE &&
                        cd $DEPLOY_BASE/$RELEASE &&

                        # clone fresh code
                        git clone -b $BRANCH $REPO . &&

                        # remove old symlink and link new release
                        rm -rf $DEPLOY_BASE/RELEASE
                        unlink $SYMLINK || true
                        ln -s $DEPLOY_BASE/$RELEASE $SYMLINK


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
                        cd /root/django-todo-react/frontend

                        # 1. Remove the broken react-scripts entry
                        npm uninstall react-scripts

                        # 2. Install the latest stable react-scripts
                        npm install react-scripts@latest --save

                        # 3. Clean and reinstall node_modules
                        rm -rf node_modules package-lock.json

                        cd $SYMLINK/frontend &&
                        npm install &&

                        sudo systemctl daemon-reload &&
                        sudo systemctl restart frontend.service
                    "
                    '''
                }
            }
        }

        stage('Verify Active Release') {
            steps {
                sshagent(['ssh-cred']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no $SERVER "
                    echo 'Cleaning up old releases...'
                    releases=\$(ls -1tr | grep '^release-')

                    echo \"All releases:\"
                    echo \"\$releases\"

                    echo \"Deleting old ones...\"
                    echo \"\$releases\" | head -n -2 | xargs -r rm -rf

                    echo 'Remaining releases:'
                    ls -1tr
                    echo 'Active release is:'
                    readlink -f $SYMLINK
                    "
                    '''
                }
            }
        }
    }
}

