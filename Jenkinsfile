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
                echo "----------Build Started----------"
                sh 'mvn clean deploy -Dmaven.test.skip=true'
                echo "---------Build Completed---------"
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
        stage("Test") {
            steps{
                echo "----------Unit Test Started----------"
                sh 'mvn surefire-report:report'
                echo "---------Unit Test Completed---------"
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

