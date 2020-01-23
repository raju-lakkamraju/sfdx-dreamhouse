import groovy.json.JsonSlurperClassic
pipeline {
    agent any
    environment { 
        SFDX_LOGIN = credentials('devhub_user')
        SFDX_PRIVATE_KEY = credentials('keyfile')
        SFDX_URL = "https://curious-raccoon-bzbdvt-dev-ed.lightning.force.com"
	SFDX_LOGIN_PWD = "3MVG9LBJLApeX_PC2OLY0P9EKpf.C6D9RMSrkNA7ymOrYXx0mi82ZJN4ookm6lzGVHRM0uAYrRb1TnoOJrPxT"
	SFDX_LOGIN_USR = "slakkamraju@visio.com"
	

    }
    stages {
        stage('Git Checkout') {
            steps {
                // Pull in the new changes from GitHub
                checkout scm
            }
        }

        stage('Authenticate SFDX') {
            steps {
                script {
                    rc = sh (returnStatus: true, script: "sfdx force:auth:jwt:grant --clientid ${SFDX_LOGIN_PSW} --username ${SFDX_LOGIN_USR} --jwtkeyfile ${SFDX_PRIVATE_KEY} --instanceurl ${SFDX_URL}")
                    
                    if (rc != 0) {
                         error 'Failed to authorize Salesforce DX' 
                    }
                }
            }
        }
    }
}
