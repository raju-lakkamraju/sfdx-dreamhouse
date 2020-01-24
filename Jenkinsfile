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
	SCRATCH_ORG_NAME = "sample1_ci_test"

	BRANCH = env.BRANCH_NAME.replaceAll(/[\/\\]/, "")
	

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
		   echo "on branch name: ${BRANCH}"
		   echo "---------Authorization started"
                    rc = sh returnStatus: true, script: "${SFDX_CLIENT_LIBRARIES_HOME}/sfdx force:auth:jwt:grant --clientid ${SFDX_CONNECTED_APP_CONSUMER_KEY} --username ${SFDX_DEVHUB_LOGIN_USER} --jwtkeyfile ${SFDX_PRIVATE_KEY} --instanceurl ${SFDX_DEVHUB_URL} --loglevel debug"
                    
                    if (rc != 0) {
                         error 'Failed to authorize Salesforce DX' 
                    } else {
			echo "Visio - Successfully authorized to DEV HUB ${SFDX_DEVHUB_URL}"
		    }
		   echo "---------Authorization ended"
                }
            }
        }
	stage('Create Scratch Org') {
            steps {
                script {
		    echo "---------Scrach org creation started"
                    // need to pull out assigned username
                    rmsg = sh returnStdout: true, script: "${SFDX_CLIENT_LIBRARIES_HOME}/sfdx force:org:create -s --definitionfile config/project-scratch-def.json --json --targetdevhubusername ${SFDX_DEVHUB_LOGIN_USER} -d 1" 
                    //rmsg = sh returnStdout: true, script: "${SFDX_CLIENT_LIBRARIES_HOME}/sfdx force:org:create -s --definitionfile config/project-scratch-def.json -a ${SCRATCH_ORG_NAME} --json --targetdevhubusername ${SFDX_DEVHUB_LOGIN_USER}" 
                    echo "${rmsg}"
                    def robj = readJSON text: rmsg
                    if (robj.status != 0) { 
                        error 'Scratch org creation failed: ' + robj.message 
                    } else {
		        echo "Visio - Scratch Org is Successfully created. Returned Message is:  " + robj.message
		    }
                    SFDC_USERNAME=robj.result.username
		    echo "Username for scrach org: ${SFDC_USERNAME}"
                    robj = null
		    echo "---------Scrach org creation ended"
                }
            }
        }
	stage('Push To Scratch Org') {
            steps {
                script {
		    echo "---------Push to Scrach org started"
                    rc = sh returnStdout: true, script: "${SFDX_CLIENT_LIBRARIES_HOME}/sfdx force:source:push --targetusername ${SFDC_USERNAME}" 
		    if (rc != 0) {
			error 'Failed pushing to scrach org (username: ${SFDC_USERNAME})'
		    } else {
			echo "Visio - Successfully pushed to scrach org (username: ${SFDC_USERNAME})"
		    }
		    echo "---------Push to Scrach org ended"
		}
	    }
	}
    }
}
