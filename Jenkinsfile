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
                    echo 'checking commit'
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
