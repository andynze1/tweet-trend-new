pipeline {
    agent {
        node {
            label 'maven'
        }
    }

    stages {
        stage('Clean') {
            steps {
                // Remove all contents from the workspace directory without using sudo
                deleteDir()
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean deploy'
            }
        }
        stage('Clean /tmp Directory') {
            steps {
                // Remove files from the /tmp directory
                sh 'rm -rf /tmp/*'
            }
        }
        stage('Check space') {
            steps {
                sh 'df -h'
            }
        }
    }
}

