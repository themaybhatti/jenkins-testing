pipeline {
    agent any
    environment {
        awsAccount = "APDIntegrationProd"
        deployEnv = "APDProd"
        awsAccountID = "654654615177"
    }
    stages {
        stage('Setup') {
            steps {
                echo 'Setup...'
                script {
                    echo 'Updating jenkins build display...'
                    currentBuild.displayName = "${BUILD_NUMBER} [${env.deployEnv}] [${awsAccountID}]"
                    currentBuild.description = "Deploying AMI to AWS account ${awsAccount}"

                    checkout scm
                }
                echo 'Additional Dependencies...'
                script {
                    sh '''
                        cd /tmp
                        wget -q https://releases.hashicorp.com/packer/1.10.2/packer_1.10.2_linux_amd64.zip
                        unzip /tmp/packer_1.10.2_linux_amd64.zip
                        sudo cp packer /usr/local/bin
                    '''
                }
            }
        }
    }
    post {
        success {
            echo "Build succeeded"
        }
        failure {
            echo "Build failed"
        }
        cleanup {
            cleanWs(
                deleteDirs: true,
                disableDeferredWipeout: true
            )
        }
    }
}
