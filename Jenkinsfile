pipeline { 
   agent { label ' jenlins-agent ' }
  tools {
    jdk 'Java17' 
    maven 'Maven3'
  }
   environment {
      APP_NAME = "net-pipeline"
      RELEASE = "1.0.0"
      DOCKER_USER = "kushal9897"
      DOCKER_PASS = 'dockerhub'
      IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
      IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
       JENKINS_API_TOKEN = credentials("JENKINS_API_TOKEN")
   }
  stages {
    stage("Cleanup Workspace"){
      steps {
        cleanWs ()
      }
    }
    stage("Checkout from SCM"){
      steps {
        git branch: 'main' , credentialsId: 'github' , url: 'https://github.com/kushal9897/net.git'
      }
    }
    stage ("Build Application" ) {
      steps{
        sh "mvn clean package"
      }
    }
    stage("Test Application" ) {
      steps {
        sh "mvn test "
      }
      
    }

     stage("SonarQube Analysis"){
        steps {
           script{
              withSonarQubeEnv(credentialsId: 'jenlins-sonarqube-token') {
                 sh "mvn sonar:sonar"
              }
           }
        }
     }

     stage("Quality Gate"){
        steps{
           script {
              waitForQualityGate abortPipeline: false, credentialsId: 'jenlins-sonarqube-token'
           }
        }
     }

     stage("Build & Push Docker Image"){
        steps{
           script{
              docker.withRegistry('',DOCKER_PASS){
                 docker_image = docker.build "${IMAGE_NAME}"
           }
              docker.withRegistry('',DOCKER_PASS){
                 docker_image.push("${IMAGE_TAG}")
                 docker_image.push('latest')
              }
          }
        }
     }

     stage("Trivy Scan") {
           steps {
               script {
	            sh ('docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image kushal9897/net-pipeline:latest --no-progress --scanners vuln  --exit-code 0 --severity HIGH,CRITICAL --format table')
               }
           }
       }

      stage ('Cleanup Artifacts') {
           steps {
               script {
                    sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker rmi ${IMAGE_NAME}:latest"
               }
          }
       }

	  stage("Trigger CD Pipeline") {
            steps {
                script {
                    sh "curl -v -k --user clouduser:${JENKINS_API_TOKEN} -X POST -H 'cache-control: no-cache' -H 'content-type: application/x-www-form-urlencoded' --data 'IMAGE_TAG=${IMAGE_TAG}' 'ec2-16-16-185-112.eu-north-1.compute.amazonaws.com:8080/job/gitops-net-cd/buildWithParameters?token=gitops-token'"
		}
	    }
	  }
	  
   }
}
