pipeline { 
   agent { label ' jenlins-agent ' }
  tools {
    jdk 'Java17' 
    maven 'Maven3'
  }

  stages {
    stage("Cleanup Workspace"){
      steps {
        cleanWs ()
      }
    }
    stage("Checkout form SCM"){
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
     stage('SonarQube Analysis') {
            environment {
                scannerHome = tool 'SonarQubeScanner' // This should be the name of your SonarQube scanner installation in Jenkins
            }
            steps {
                withSonarQubeEnv('SonarQubeServer') { // This should be the name of your SonarQube server configuration in Jenkins
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }
   }
}
