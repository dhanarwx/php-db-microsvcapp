pipeline{
    agent any
    environment{
        IMAGE_NAME='devopstrainer/java-mvn-privaterepos:php$BUILD_NUMBER'
        DEV_SERVER_IP='ec2-user@65.0.97.181'
        TEST_SEVER_IP='ec2-user@3.110.56.23'
    }
    stages{
        stage('Build the dockerimage for php and push to dockerhub on DEV_SERVER'){
            steps{
                script{
                    sshagent(['DEV_SERVER']) {
                        withCredentials([usernamePassword(credentialsId: 'docker-hub', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]) {
                         echo "building teh dockerimage"
                         sh "scp -o strictHostKeyChekcing=no -r devserverconfig ${DEV_SERVER_IP}:/home/ec2-user"
                         sh "ssh -o strictHostKeyChekcing=no ${DEV_SERVER_IP} 'bash ~/devserverconfig/docker-script.sh'"
                         sh "ssh ${DEV_SERVER_IP} sudo docker build -t ${IMAGE_NAME} /home/ec2-user/devserverconfig"
                         sh "ssh ${DEV_SERVER_IP} sudo docker login -u $USERNAME -p $PASSWORD"
                         sh "ssh ${DEV_SERVER_IP} sudo docker push ${IMAGE_NAME}"

                        }

                    }
                }
            }


        }
        stage('run the php_db app on test server on TEST_SERVER'){
             steps{
                script{
                    sshagent(['TEST_SERVER']) {
                        withCredentials([usernamePassword(credentialsId: 'docker-hub', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]) {
                         echo "building teh dockerimage"
                         sh "scp -o strictHostKeyChekcing=no -r testserverconfig ${TEST_SERVER_IP}:/home/ec2-user"
                         sh "ssh -o strictHostKeyChekcing=no ${TEST_SERVER_IP} 'bash ~/testserverconfig/docker-ascript.sh'"
                         sh "ssh ${TEST_SERVER_IP} sudo docker login -u $USERNAME -p $PASSWORD"
                         sh "ssh ${TEST_SERVER_IP} bash /home/ec2-user/testserverconfig/compose-script.sh ${IMAGE_NAME}"

                        }

                    }
                }
            }


        }
    }
}