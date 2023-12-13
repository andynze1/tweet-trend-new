pipeline {
    agent {
        node {
            label 'maven'
        }
    }
// environment {
//     PATH = "/opt/apache-mvn"
// }

    stages {
        // stage('Clean') {
        //     steps {
        //         // Remove all contents from the workspace directory without using sudo
        //         deleteDir()
        //     }
        // }
        stage('Build') {
            steps {
                sh 'mvn clean deploy'
            }
        }
        stage('Clean /tmp Directory') {
            steps {
                // Remove files from the /tmp directory
                sh 'sudo rm -rf /tmp/*'
            }
        }
        stage('Check space') {
            steps {
                sh 'df -h'
            }
        }

    stage('SonarQube analysis') {
    environment {
     scannerHome = tool 'andynze-sonar-scanner'
    }
    steps{

    withSonarQubeEnv('andynze-sonarqube-server') { // If you have configured more than one global server connection, you can specify its name
      sh "${scannerHome}/bin/sonar-scanner"
    }
  }
}
 }
}

