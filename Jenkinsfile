
@Library(['global_variables_library@prod-mnt','rundeck-shared-library@Branchv1.2']) _
  def serviceTypeMap = libraryResource "applicationServiceTypeMap.properties"
def serviceList=[];
if(serviceTypeMap != null){
     def envVar=serviceTypeMap.split("\n");
     envVar.each{
      def val=it.split("=");
     serviceList.add("${val[0]}");
     }
  }
pipeline{
    agent { label 'cx-agent' }
		parameters {
			choice(name: 'Application', choices: 'DDINS-RSO')
        	string(name: 'RELEASE', defaultValue: 'MNT.')
			choice(name: 'only_property_file_deployment?', choices: ['no','yes'])
        	choice(name: 'domain_name', choices: ['Primary','secondary'])
        	choice(name: 'Env', choices: 'PROD')
		}

    stages{
        stage('Load Poperties') {
			steps{ script{ globalTemplate.loadVariables() }
			    sh 'env | sort '}
			}
	
		stage('RM Approval'){
		    when {
    	        expression {params.DEPLOYMENT_ENVIRONMENT == 'PROD'}
    	    }
    	      	steps{
    	           	timeout(time: 20, unit: 'MINUTES') {
    	        	  	input(message: "Do you approve this  deployment Operation in ${Env} environment ? Click PROCEED or ABORT.")
    	        	}
    	    	}
		}
    	stage('update property tag') {
    	    steps {
    	        script{
    	            removeTag("${RELEASE}","${ENV}/${Application}")
    	            try
    	            {
					removeTag("${RELEASE}","${ENV}-private/${Application}")
    	            }
    	            gitTagging("${RELEASE}","${ENV}/${Application}")
    	        }
    	  	}
   		}
   
		stage("Deploy"){
    	    steps{
    	        timeout(time: 20, unit: 'MINUTES') {
    	          input(message: "Do you approve this deployment? Click PROCEED or ABORT.")
    	        }
    	    	script {
					if (params.only_property_file_deployment?? == 'yes') && ( params.domain_name == 'primary') {

						rundeckLibrary.deployJobWithPram("66ab500b-c0b6-4d37-af9a-ccce5d90e03c", """release="${RELEASE}"
   							application=${SERVICE}""")

					}
					else if (params.only_property_file_deployment?? == 'yes') && ( params.domain_name == 'secondary') {

						rundeckLibrary.deployJobWithPram("66ab500b-c0b6-4d37-af9a-ccce5d90e03c", """release="${RELEASE}"
   							application=${SERVICE}""")

					}
					else if (params.only_property_file_deployment?? == 'no') && ( params.domain_name == 'primary') {

						rundeckLibrary.deployJobWithPram("66ab500b-c0b6-4d37-af9a-ccce5d90e03c", """release="${RELEASE}"
   							application=${SERVICE}""")

					}
					else if (params.only_property_file_deployment?? == 'no') && ( params.domain_name == 'secondary') {

						rundeckLibrary.deployJobWithPram("66ab500b-c0b6-4d37-af9a-ccce5d90e03c", """release="${RELEASE}"
   							application=${SERVICE}""")

					}

    			}
			}
    	}
    }
}
