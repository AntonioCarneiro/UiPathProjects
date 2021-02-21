pipeline {
            agent any
  	        // Environment Variables
	        environment {
	        MAJOR = '1'
	        MINOR = '0'

	        // Orchestrator Services UAT
	        UIPATH_ORCH_URL = "https://cloud.uipath.com/"
	        UIPATH_ORCH_LOGICAL_NAME = "CarneiroUiPathi"
	        UIPATH_ORCH_TENANT_NAME = "CertificateUiPath"
	        UIPATH_ORCH_FOLDER_NAME = "Default"

	        // Orchestrator Services PRD
	        UIPATH_ORCP_URL = "https://cloud.uipath.com/"
	        UIPATH_ORCP_LOGICAL_NAME = "testdev_cloud"
	        UIPATH_ORCP_TENANT_NAME = "Development"
	        UIPATH_ORCP_FOLDER_NAME = "Default"

	        UIPATH_ORCA_EMAIL = "carneiro.ac@gmail.com"
	    }
	    stages {
	        // Printing Basic Information
	        stage('Preparing'){
	            steps {
	                echo "Jenkins Home ${env.JENKINS_HOME}"
	                echo "Jenkins URL ${env.JENKINS_URL}"
	                echo "Jenkins JOB Number ${env.BUILD_NUMBER}"
	                echo "Jenkins JOB Name ${env.JOB_NAME}"
	                echo "GitHub BranhName ${env.BRANCH_NAME}"
	                checkout scm
	            }
	        }
	         // Build Stages
	        stage('Build') {
	            steps {
	                echo "Building..with ${WORKSPACE}"
	                UiPathPack (
	                      outputPath: "Output\\${env.BUILD_NUMBER}",
	                      projectJsonPath: "project.json",
	                      version: [$class: 'ManualVersionEntry', version: "${MAJOR}.${MINOR}.${env.BUILD_NUMBER}"],
	                      useOrchestrator: false
        	        )
	            }
	        }
	         // Test Stages
	        stage('Test') {
	            steps {
	                echo 'Testing..the workflow here...'
	            }
	        }
            // Deploy Stages
	        stage('Deploy to UAT') {
	            steps {
	                echo "Deploying ${BRANCH_NAME} to UAT "
	                UiPathDeploy (
	                 packagePath: "Output\\${env.BUILD_NUMBER}",
	                 orchestratorAddress: "${UIPATH_ORCH_URL}",
	                 orchestratorTenant: "${UIPATH_ORCH_TENANT_NAME}",
	                 folderName: "${UIPATH_ORCH_FOLDER_NAME}",
	                 environments: 'DEV',
	                 //credentials: [$class: 'UserPassAuthenticationEntry', credentialsId: 'APIUserKey']
	                 credentials: Token(accountName: "${UIPATH_ORCH_LOGICAL_NAME}", credentialsId: 'APIUserKey'), 
	                )
	            }
	        }
            // Deploy to Production Step
	        stage('Deploy to Production') {
	            steps {
	                echo "Deploying ${BRANCH_NAME} to Production"
	                UiPathDeploy (
	                 packagePath: "Output\\${env.BUILD_NUMBER}",
	                 orchestratorAddress: "${UIPATH_ORCP_URL}",
	                 orchestratorTenant: "${UIPATH_ORCP_TENANT_NAME}",
	                 folderName: "${UIPATH_ORCP_FOLDER_NAME}",
	                 environments: 'PRD',
	                 //credentials: [$class: 'UserPassAuthenticationEntry', credentialsId: 'APIUserKey']
	                 credentials: Token(accountName: "${UIPATH_ORCP_LOGICAL_NAME}", credentialsId: 'UserAPIKeyPRD'),
	                )
	            }
	        }
	    }
	    // Options
	    options {
	        // Timeout for pipeline
	        timeout(time:80, unit:'MINUTES')
	        skipDefaultCheckout()
	    }

	    // 
	    post {
	        success {
	            echo 'Deployment has been completed!'
	            mailUser("${UIPATH_ORCA_EMAIL}", "Finished")
	        }
	        failure {
	          echo "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.JOB_DISPLAY_URL})"
	        }
	        always {
	            /* Clean workspace if success */
	            cleanWs()
	        }
	    }
	}