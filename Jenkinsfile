
pipeline {
    agent any

    environment {
        DEPLOY_BASE = "/var/www/releases"         // all release folders go here
        SYMLINK = "/root/django-todo-react"       // services point here
        RELEASE = "release-${BUILD_NUMBER}"       // new release folder name
        REPO = "https://github.com/GAURAVJAIN2498/django-todo-react.git"
        BRANCH = "main"
        SERVER = "root@3.80.52.145"        
        FRONTEND = "/var/www/frontend"   
        FRONTEND_RELEASE= "/var/www/frontend/releases/${RELEASE}"    // prod server
    }

    stages {
        stage('Deployment Code') {
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

                        # update symlink to point to new release
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

                        # Create frontend release folder
                        FRONTEND_RELEASE=$FRONTEND/releases/$RELEASE
                        mkdir -p $FRONTEND_RELEASE

                        # Build React app
                        cd $SYMLINK/frontend
                        npm uninstall react-scripts
                        npm install react-scripts@latest --save
                        rm -rf node_modules package-lock.json
                        npm install
                        npm run build

                        # Move build to release folder
                        cp -r build/* $FRONTEND_RELEASE/

                        # Update 'current' symlink to latest release
                        ln -sfn $FRONTEND_RELEASE $FRONTEND/current

                        # Reload Nginx
                        sudo systemctl reload nginx
                       "
            '''
        }
    }
}

        stage('Cleanup Old Releases') {
            steps {
                sshagent(['ssh-cred']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no $SERVER "
                        set -e

                        cd $DEPLOY_BASE

                        echo 'Cleaning up old releases...'
                        ls -1tr | grep '^release-' | head -n -2 | xargs -r rm -rf

                        echo 'Remaining releases:'
                        ls -1tr

                        echo 'Active release is:'
                        readlink -f $SYMLINK

                        cd $FRONTEND/releases
                        ls -1tr | head -n -2 | xargs -r rm -rf

                    "
                    '''
                }
            }
        }
    }
}
