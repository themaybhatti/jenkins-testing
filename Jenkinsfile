pipeline {
    agent any
    environment {
        awsAccount = "APDIntegrationProd"
        deployEnv = "APDProd"
        awsAccountID = "654654615177"
        // serviceAccountId = "ssh_scmtfsuser_ro" 
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
        stage('Build AMI image'){
            environment {
                vault_cred = credentials('apd_account')
                vault_token = sh(script: 'set +x; vault write -field=token -tls-skip-verify -address=${VAULT_HTTPS_URL} auth/approle/login role_id=${vault_cred_USR} secret_id=${vault_cred_PSW}', returnStdout: true).trim()
            }
            steps {
                echo 'Get and update AMI image...'
                script {
                    sh '''
                        // export PKR_VAR_awsAccount="${awsAccount}"
                        // export PKR_VAR_BUILD_NUMBER="${BUILD_NUMBER}"
                        // eval \$(ssh-agent -s)
                        // mkdir -p ~/.ssh
                        // chmod 2700 ~/.ssh
                        // ssh-keyscan tfs.mitchell.com >> ~/.ssh/known_hosts
                        // set +x
                        // export AccountNumber="${awsAccountID}"
                        source ${WORKSPACE}/jobScripts/aws_assumed_cicd_credentials.sh 

                        // ssh-add ${serviceAccountId}
                        cd ${WORKSPACE}
                        ansible-galaxy collection install microsoft.ad
  
                        PACKER_LOG=1 packer build -var="build_number=${BUILD_NUMBER}" packer.pkr.hcl
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