pipeline {
    agent {
        node {
            label 'maven'
        }
    }
environment {
    PATH = "/opt/apache-mvn"
}

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
    }
}

