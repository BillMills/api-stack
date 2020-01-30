pipeline {    

    agent any    
    
    environment {
        DOCKER_TLS_VERIFY='1'                                                                                               
        COMPOSE_TLS_VERSION='TLSv1_2'                                                                                       
        DOCKER_CERT_PATH='/home/jenkins/jenkinscerts'
        DOCKER_HOST='tcp://ec2-3-218-141-222.compute-1.amazonaws.com:443'
        DTR_FQDN_PORT='ec2-3-218-141-222.compute-1.amazonaws.com:4443'
    }

    stages {
        stage('Build') {
            environment {
                DTR_ACCESS_KEY = credentials('jenkins-dtr-access-token')
            }
	steps {
	    sh 'docker image build -t \
	        ${DTR_FQDN_PORT}/engineering/api-build:rc-1.0-build-${BUILD_ID} \
	        api'
	    sh 'docker image build --target test -t \
	        ${DTR_FQDN_PORT}/engineering/api-build:unittest-1.0-build-${BUILD_ID} \
	        api'
	    sh 'docker image build -t \
	        ${DTR_FQDN_PORT}/engineering/api-build:integration-1.0-build-${BUILD_ID} \
	        api/integration'
	    sh 'docker login -u jenkins -p ${DTR_ACCESS_KEY} ${DTR_FQDN_PORT}'
	    sh 'docker image push \
	        ${DTR_FQDN_PORT}/engineering/api-build:rc-1.0-build-${BUILD_ID}'
	    sh 'docker image push \
	        ${DTR_FQDN_PORT}/engineering/api-build:unittest-1.0-build-${BUILD_ID}'
	    sh 'docker image push \
	        ${DTR_FQDN_PORT}/engineering/api-build:integration-1.0-build-${BUILD_ID}'
	}
        }
    }
	
    post {
        always{
	        sh 'rm -rf /var/jenkins_home/workspace/build-server/*'
        }
    }
}
