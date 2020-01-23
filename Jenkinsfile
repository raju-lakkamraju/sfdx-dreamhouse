import groovy.json.JsonSlurperClassic
pipeline {
    agent any
    environment { 
        SFDX_DEVHUB_LOGIN_CREDENTIALS = credentials('devhub_user')
        SFDX_PRIVATE_KEY = credentials('keyfile')
        SFDX_DEVHUB_URL = "https://curious-raccoon-bzbdvt-dev-ed.lightning.force.com"
	SFDX_CONNECTED_APP_CONSUMER_KEY = "3MVG9LBJLApeX_PC2OLY0P9EKpf.C6D9RMSrkNA7ymOrYXx0mi82ZJN4ookm6lzGVHRM0uAYrRb1TnoOJrPxT"
	SFDX_DEVHUB_LOGIN_USER = "slakkamraju@visio.com"
	SFDX_CLIENT_LIBRARIES_HOME = "/usr/local/bin"
	

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
                    rc = sh (returnStatus: true, script: "${SFDX_CLIENT_LIBRARIES_HOME}/sfdx force:auth:jwt:grant --clientid ${SFDX_CONNECTED_APP_CONSUMER_KEY} --username ${SFDX_DEVHUB_LOGIN_USER} --jwtkeyfile ${SFDX_PRIVATE_KEY} --instanceurl ${SFDX_DEVHUB_URL}")
                    
                    if (rc != 0) {
                         error 'Failed to authorize Salesforce DX' 
                    }
                }
            }
        }
	stage('Create Scratch Org') {
            steps {
                script {
                    // need to pull out assigned username
                    rmsg = sh returnStdout: true, script: "sfdx force:org:create --definitionfile config/project-scratch-def.json --json --targetdevhubusername ${SFDX_DEVHUB_LOGIN_USER}"
                    echo "${rmsg}"
                    def robj = readJSON text: rmsg
                    if (robj.status != 0) { 
                        error 'Scratch org creation failed: ' + robj.message 
                    }
                    SFDC_USERNAME=robj.result.username
                    robj = null
                }
            }
        }
    }
}
