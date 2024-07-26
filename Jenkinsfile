pipeline { 
   agent { label ' jenkins-agent ' }
  tools {
    jdk ' Java17 ' 
    maven ' Maven3 '
  }

  stages {
    stage("Cleanup Workspace"){
      steps {
        cleanWs ()
      }
    }
    stage("Checkout form SCM"){
      steps {
        git branch: 'main' , credentials Id: 'github' , url: 'https://github.com/kushal9897/net.git'
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
   }
}
