pipeline {
    agent any

    environment {
        EC2_HOST = "13.206.241.223"
        APP_NAME = "task-manager-lp2"
        CONTAINER_NAME = "task-manager-lp2-container"
        MONGODB_URI = credentials('mongodb-uri')
    }

    stages {
        stage('Deploy on EC2') {
            steps {
                sshagent(['ec2-ssh-key']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@$EC2_HOST "
                        if [ ! -d $APP_NAME ]; then
                            git clone https://github.com/4SNA/Task-Manager-LP2.git $APP_NAME
                        fi

                        cd $APP_NAME
                        git pull origin main

                        docker stop $CONTAINER_NAME || true
                        docker rm $CONTAINER_NAME || true

                        docker build -t $APP_NAME:latest .

                        docker run -d \
                        --name $CONTAINER_NAME \
                        -p 5000:5000 \
                        -e PORT=5000 \
                        -e MONGODB_URI='$MONGODB_URI' \
                        $APP_NAME:latest
                    "
                    '''
                }
            }
        }
    }
}
