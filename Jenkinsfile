pipeline {
    agent any
    environment {
		RELEASE_DATE = new Date().format('yy.M')
		BUILD_NUM = "${RELEASE_DATE}.${BUILD_NUMBER}"
		DOCKER_REPO="chinmaydas"
        DOCKER_IMG_NAME="webapp"
	
    }

    options {
       
        timeout(time: 30, unit: "MINUTES")
        disableConcurrentBuilds()
        buildDiscarder(logRotator(numToKeepStr: '25'))

    }
    /*
    triggers {
	
    }
    */
    stages {
		stage ('Checkout Code')
		{
			steps {
				echo "Checkout ${BUILD_NUM}"
			}
		}
  /*      stage('Build Code')
 #       {
 #           steps {
				echo "Build Code ${BUILD_NUM}"
                sh "cd AppCode && mvn clean package"
			}
        }
*/
    	stage('Build Docker Image')
		{
			
			steps {
				echo "Build Docker Image"
				sh "docker -v"
                sh "docker build . -t ${DOCKER_REPO}/${DOCKER_IMG_NAME}:${BUILD_NUM}"

			}
		}
        stage('Push Docker Image')
		{
			
			steps {
				echo "Push Docker Image "
				sh "docker login --username=chinmaydas --password=Redhat@123"
                sh "docker push ${DOCKER_REPO}/${DOCKER_IMG_NAME}:${BUILD_NUM}"

			}
		}
        stage('Deploy to K8S Cluster')
        {
            steps {
				echo "Deploying to Production Server"
                kubernetesDeploy(kubeconfigId: 'KUBECONFIG',               // REQUIRED
                 configs: 'depconfig/deployment.yaml', // REQUIRED
                 enableConfigSubstitution: true,
                            
                 dockerCredentials: [
                        [credentialsId: 'dockerhubcred']
                        
                 ]
                )

			}
        }
   	
		
    }
	post {

        success {
		    echo "Send Success Notification"
		   
			
		}
        failure {
			echo "Send Failure Notification"
		 
		    
        }
        always {
			
            deleteDir()
            cleanWs()
           
        }
    }
}
