def registry = 'https://dml003.jfrog.io/'
def imageName = 'dml003.jfrog.io/dml003-docker-local/ttrend'
def version   = '2.1.2'
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
                // Remove files from the /tmp directory.
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
     scannerHome = tool 'sonar-scanner'
    }
    steps{

    withSonarQubeEnv('sonarqube-server') { // If you have configured more than one global server connection, you can specify its name
      sh "${scannerHome}/bin/sonar-scanner"
    }
  }
}
    stage("Quality Gate"){
      steps {
        script {
        timeout(time: 1, unit: 'HOURS') { // Just in case something goes wrong, pipeline will be killed after a timeout
      def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
      if (qg.status != 'OK') {
        error "Pipeline aborted due to quality gate failure: ${qg.status}"
      }
     }
    }
   }
  }
    stage("Jar Publish") {
        steps {
            script {
                echo '<--------------- Jar Publish Started --------------->'
                     def server = Artifactory.newServer url:registry+"/artifactory" ,  credentialsId:"jfrog-auth-cred"
                     def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
                     def uploadSpec = """{
                          "files": [
                            {
                              "pattern": "jarstaging/(*)",
                              "target": "dml003-libs-release-local/{1}",
                              "flat": "false",
                              "props" : "${properties}",
                              "exclusions": [ "*.sha1", "*.md5"]
                            }
                         ]
                     }"""
                     def buildInfo = server.upload(uploadSpec)
                     buildInfo.env.collect()
                     server.publishBuildInfo(buildInfo)
                     echo '<--------------- Jar Publish Ended --------------->'          
                }
            }   
        }
    stage(" Docker Build ") {
      steps {
        script {
           echo '<--------------- Docker Build Started --------------->'
           app = docker.build(imageName+":"+version)
           echo '<--------------- Docker Build Ends --------------->'
        }
      }
    }

    stage (" Docker Publish "){
        steps {
            script {
               echo '<--------------- Docker Publish Started --------------->'  
                docker.withRegistry(registry, 'jfrog-auth-cred'){
                    app.push()
                }    
               echo '<--------------- Docker Publish Ended --------------->'  
            }
        }
    }
    }
}